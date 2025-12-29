# Security

Last updated: **{{ git_revision_date_localized }}**

While the Public cloud team and the Security Operations team provide the initial guardrails and standards for the environment, **each ministry team is responsible** for managing their own resources and ensuring compliance with the standards.

---

## Defender for Cloud

Microsoft Defender for Cloud is a Cloud Native Application Protection Platform (CNAPP), which is a unified solution that combines multiple cloud security tools to protect applications across their entire lifecycle. The solution provides a comprehensive view of your security posture across your cloud and on-premises resources.

Within the Azure portal, navigate to [Microsoft Defender for Cloud](https://portal.azure.com/#view/Microsoft_Azure_Security/SecurityMenuBlade/~/0). This service provides a centralized view of the security posture and regulatory compliance of your Azure environment. It also provides recommendations on how to improve your security.

![Defender for Cloud Security Posture](../images/defender-for-cloud-security-posture.png "Defender for Cloud Security Posture")

![Defender for Cloud Regulatory Compliance](../images/defender-for-cloud-regulatory-compliance.png "Defender for Cloud Regulatory Compliance")

!!! warning "Just-In-Time (JIT) VM Access Recommendations"
    Microsoft Defender for Cloud may provide recommendations to "Enable Just-In-Time VM access" to reduce the attack surface of your virtual machines. 
    
    **Please ignore this specific recommendation.** 
    
    JIT access is intended for VMs with public IP addresses. In the BC Gov Landing Zone, public IPs are blocked by guardrails. Enabling JIT will create Network Security Group (NSG) rules that can block internal RDP/SSH traffic, effectively breaking your [Azure Bastion](../tools/bastion.md) connectivity.
