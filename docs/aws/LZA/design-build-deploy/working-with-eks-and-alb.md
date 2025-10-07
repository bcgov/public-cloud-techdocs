# EKS + Internal Load Balancers in LZA

This guide explains, at a high level, the two supported ways to expose EKS apps through an internal ALB in LZA, and provides Terraform/YAML snippets as an example.

## Key LZA requirements to highlight up front

- Tag every internal ALB with Public=True (used by our perimeter automation).

- Ensure the internal ALB has a listener rule for path /bcgovhealthcheck that returns HTTP 200 (fixed response). The perimeter public ALB will probe this.

- SCPs block creating IAM OIDC providers in LZA. Use EKS Pod Identity (addon + association) instead of IRSA to give the AWS Load Balancer Controller permissions.

## Two patterns

- Kubernetes‑managed ALB via AWS Load Balancer Controller + Ingress (share one ALB using an Ingress Group).

- Terraform‑managed ALB (prebuilt) + TargetGroupBinding (Kubernetes only registers pods into Terraform target groups).

## Pattern A — Kubernetes‑managed internal ALB (Ingress + LBC)

High level: Use the AWS Load Balancer Controller (LBC) so Ingress creates/owns an internal ALB. Reuse the same ALB across apps by sharing an Ingress Group name.

LZA callouts:

- Add ALB tag Public=True.

- Add a fixed 200 rule for /bcgovhealthcheck (perimeter health probe).

- Use EKS Pod Identity (SCPs block OIDC provider creation/IRSA).

## Minimal Terraform snippets only

## Pod Identity add-on (no OIDC needed)

```hcl
resource "aws_eks_addon" "pod_identity" {
  cluster_name = aws_eks_cluster.this.name
  addon_name   = "eks-pod-identity-agent"
}
```

## Ingress (creates ALB)

```hcl
# Ingress (internal ALB; shared group; fixed 200 at /bcgovhealthcheck)
resource "kubernetes_ingress_v1" "internal_alb" {
  metadata {
    name      = "sample-internal-alb"
    namespace = var.app_namespace
    annotations = {
      "kubernetes.io/ingress.class"                  = "alb"
      "alb.ingress.kubernetes.io/group.name"         = "internal-apps"
      "alb.ingress.kubernetes.io/scheme"             = "internal"
      "alb.ingress.kubernetes.io/subnets"            = join(",", data.aws_subnets.web.ids)   # Web subnets
      "alb.ingress.kubernetes.io/security-groups"    = data.aws_security_group.web_sg.id     # Existing ALB SG
      "alb.ingress.kubernetes.io/target-type"        = "ip"
      "alb.ingress.kubernetes.io/certificate-arn"    = var.acm_cert_arn
      "alb.ingress.kubernetes.io/listen-ports"       = "[{\"HTTPS\":443}]"
      "alb.ingress.kubernetes.io/tags"               = "Public=True"   # required by perimeter automation
      "alb.ingress.kubernetes.io/manage-backend-security-group-rules" = "true"
      "alb.ingress.kubernetes.io/actions.healthz" = jsonencode({
        Type = "fixed-response",
        FixedResponseConfig = { StatusCode = "200", ContentType = "text/plain", MessageBody = "ok" }
      })
    }
  }
  spec {
    rule {
      http {
        # Perimeter health (served by ALB)
        path { path = "/bcgovhealthcheck" path_type = "Exact"
          backend { service { name = "healthz" port { name = "use-annotation" } } } }

        # App traffic → Service:80 (keep TG health check on "/")
        path { path = "/" path_type = "Prefix"
          backend { service { name = kubernetes_service_v1.echo.metadata[0].name port { number = 80 } } } }
      }
    }
  }
}
```

## Pattern B — Using Internal Load Balancer created outside of EKS

- Below terraform snippet creates an internal ALB with a target group and later we use the TargetGroupBinding to register pods into that target group.

```hcl
# Internal ALB (Web subnets, existing ALB SG)
resource "aws_lb" "internal" {
  name               = "sample-internal-alb"
  internal           = true
  load_balancer_type = "application"
  subnets            = ["subnet-web-a", "subnet-web-b"]   
  security_groups    = ["sg-alb-web"]                     
  tags = { Public = "True" }                              # ← required by perimeter automation
}

# HTTPS listener on 443 (use your ACM certificate)
resource "aws_lb_listener" "https" {
  load_balancer_arn = aws_lb.internal.arn
  port              = 443
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-TLS13-1-2-2021-06"
  certificate_arn   = var.acm_cert_arn
  default_action {
    type = "fixed-response"
    fixed_response { status_code = "404" content_type = "text/plain" message_body = "not found" }
  }
}

resource "aws_lb_target_group" "echo" {
  name        = "echo-tg"
  vpc_id      = var.vpc_id
  port        = 80
  protocol    = "HTTP"
  target_type = "ip"                                      

  health_check {
    path                = "/"
    interval            = 15
    timeout             = 5
    healthy_threshold   = 2
    unhealthy_threshold = 2
    matcher             = "200-399"
  }
}
# Perimeter health probe: fixed 200 at /bcgovhealthcheck
resource "aws_lb_listener_rule" "health_fixed_200" {
  listener_arn = aws_lb_listener.https.arn
  priority     = 10
  action {
    type = "fixed-response"
    fixed_response { status_code = "200" content_type = "text/plain" message_body = "ok" }
  }
  condition { path_pattern { values = ["/bcgovhealthcheck"] } }
}
```

## Kubernetes (TargetGroupBinding )

- Using Manifest yaml files:

```yaml
apiVersion: elbv2.k8s.aws/v1beta1
kind: TargetGroupBinding
metadata:
  name: echo-tgb
  namespace: bcgov-eks-sample-app
spec:
  targetGroupARN: arn:aws:elasticloadbalancing:ca-central-1:111122223333:targetgroup/echo-tg/abcdef1234567890
  serviceRef:
    name: echo
    port: 80
  networking:
    ingress:
      - from:
          - securityGroup:
              groupID: sg-alb-web 
        ports:
          - protocol: TCP
            port: 80
```

- Using Terraform:

```hcl
resource "kubernetes_manifest" "echo_tgb" {
  manifest = {
    apiVersion = "elbv2.k8s.aws/v1beta1"
    kind       = "TargetGroupBinding"
    metadata = {
      name      = "echo-tgb"
      namespace = "sample-app-namespace"
    }
    spec = {
      targetGroupARN = aws_lb_target_group.echo_tg.arn
      serviceRef = {
        name = kubernetes_service_v1.sample-app.metadata[0].name
        port = 80
      }

    }
  }
}
```

## References

- [GitHub repo with a sample EKS application using Kubernetes Load Balancer Controller](https://github.com/bcgov/aws-lza-samples/tree/main/eks-sample-application)
