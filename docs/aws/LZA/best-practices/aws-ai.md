# AWS AI services

Last updated: **{{ git_revision_date_localized }}**

Many of the ministry teams are exploring AWS AI services to build intelligent applications. Artificial Intelligence (AI) and Machine Learning (ML) are rapidly evolving technologies. The following are recommendations and guidance based on AWS best practices and the specific configurations available in the B.C. government's AWS Landing Zone Accelerator.

!!! tip "AWS AI best practices"

    Be sure to review AWS's comprehensive AI and ML best practices documentation, which covers architectural considerations, security measures, governance strategies, and operational excellence for AI workloads.

    AWS provides detailed guidance on responsible AI development, model governance, and MLOps practices:

    - [AWS AI/ML best practices and design patterns](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/machinelearning-pattern-list.html)
    - [AWS Well-Architected Machine Learning Lens](https://docs.aws.amazon.com/wellarchitected/latest/machine-learning-lens/welcome.html)
    - [AWS Responsible AI practices](https://aws.amazon.com/machine-learning/responsible-ai/)

## Region availability and model limitations

All AWS AI services are available in the Canada Central (`ca-central-1`) region, which is the only supported region in AWS LZA. However, there are important limitations regarding AI model availability:

!!! warning "Model marketplace restrictions"

    **Important**: The public AWS marketplace for AI models is **not available** in the B.C. government AWS environment. This significantly limits the foundation models that can be used with Amazon Bedrock.

    **Currently available models**:
    - **Amazon Titan models** (Text, Embeddings, Image, Multimodal)
    - **Mistral models** (Mistral 7B, Mixtral)

    Popular models like Anthropic Claude, Meta Llama, Cohere, and others available in the public marketplace **cannot** be used in this environment.

Before starting development, verify that your required AI models are available within these constraints. If you need access to additional models, contact the [Public Cloud Service Desk](https://citz-do.atlassian.net/servicedesk/customer/portal/3) to discuss options.

## Available AWS AI services

The following AWS AI services are commonly used by ministry teams and are fully available in Canada Central:

### Foundation models and generative AI

- **[Amazon Bedrock](https://docs.aws.amazon.com/bedrock/)**: Access to foundation models (limited to Titan and Mistral)
- **[Amazon Q Business](https://docs.aws.amazon.com/amazonq/)**: AI-powered business intelligence and chat

### Machine learning and training

- **[Amazon SageMaker](https://docs.aws.amazon.com/sagemaker/)**: Complete ML platform for building, training, and deploying models
- **[AWS DeepRacer](https://docs.aws.amazon.com/deepracer/)**: Machine learning through autonomous racing

### Language and speech

- **[Amazon Comprehend](https://docs.aws.amazon.com/comprehend/)**: Natural language processing and text analytics
- **[Amazon Translate](https://docs.aws.amazon.com/translate/)**: Neural machine translation
- **[Amazon Transcribe](https://docs.aws.amazon.com/transcribe/)**: Speech-to-text conversion
- **[Amazon Polly](https://docs.aws.amazon.com/polly/)**: Text-to-speech conversion
- **[Amazon Lex](https://docs.aws.amazon.com/lex/)**: Conversational AI chatbots and voice interfaces

### Vision and document processing

- **[Amazon Rekognition](https://docs.aws.amazon.com/rekognition/)**: Image and video analysis
- **[Amazon Textract](https://docs.aws.amazon.com/textract/)**: Document analysis and data extraction

### Search and recommendations

- **[Amazon Kendra](https://docs.aws.amazon.com/kendra/)**: Intelligent search service
- **[Amazon Personalize](https://docs.aws.amazon.com/personalize/)**: Real-time personalization and recommendations

### Code and development

- **[Amazon CodeGuru](https://docs.aws.amazon.com/codeguru/)**: AI-powered code reviews and application performance recommendations
- **[Amazon CodeWhisperer](https://docs.aws.amazon.com/codewhisperer/)**: AI coding companion (now part of Amazon Q)

!!! info "Service availability"

    All listed services are available in Canada Central region. For the most current service availability, consult the [AWS Canada Central region services list](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/).

## Deploying AI services securely

When deploying AWS AI services in the LZA environment, follow these security best practices:

### VPC and network configuration

- Deploy AI services within your VPC when possible
- Configure appropriate [security groups](https://docs.aws.amazon.com/vpc/latest/userguide/security-groups.html) to restrict access

### Access and authentication

- Use [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) instead of access keys
- Implement [least privilege access](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#grant-least-privilege)
- Use [AWS STS](https://docs.aws.amazon.com/STS/latest/APIReference/welcome.html) for temporary credentials

### Data protection

- Encrypt data at rest using [AWS KMS](https://docs.aws.amazon.com/kms/)
- Encrypt data in transit using TLS
- Store sensitive configuration in [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/) or [Systems Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)

!!! example "SageMaker deployment example"

    When deploying Amazon SageMaker in a private network, ensure you configure:

    - VPC endpoints for SageMaker API, Runtime, and Notebook instances
    - Private subnets for training and inference instances
    - Proper security group rules for notebook access
    - S3 VPC endpoint for model artifacts and data storage

    For detailed guidance, see [Amazon SageMaker in VPC documentation](https://docs.aws.amazon.com/sagemaker/latest/dg/infrastructure-connect-to-resources.html).

## Working with Amazon Bedrock

Amazon Bedrock is AWS's managed service for foundation models, but with the marketplace restrictions, your options are limited:

### Available foundation models

- **Amazon Titan Text**: Text generation and summarization
- **Amazon Titan Embeddings**: Text embeddings for search and recommendations
- **Amazon Titan Image Generator**: Image generation from text prompts
- **Amazon Titan Multimodal Embeddings**: Combined text and image embeddings
- **Mistral 7B Instruct**: Text generation and chat completion
- **Mixtral 8x7B Instruct**: Advanced text generation with mixture of experts

### Bedrock deployment considerations

- Models are accessed via API calls, not downloaded locally
- Use [Amazon Bedrock guardrails](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html) to implement safety controls
- Monitor usage and costs through [CloudWatch metrics](https://docs.aws.amazon.com/bedrock/latest/userguide/monitoring.html)
- Implement proper [IAM policies](https://docs.aws.amazon.com/bedrock/latest/userguide/security-iam.html) for model access

!!! warning "Model limitations impact"

    The restriction to Titan and Mistral models may limit certain use cases. If your application requires capabilities not available in these models, consider:

    - Training custom models using Amazon SageMaker
    - Using other AWS AI services for specific tasks (Comprehend, Textract, etc.)
    - Discussing alternative approaches with the Public Cloud team

## Monitoring AI services

AWS provides comprehensive monitoring capabilities for AI services:

### CloudWatch monitoring

- Monitor service usage, latency, and error rates
- Set up [CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) for anomaly detection
- Create [custom dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) for AI service metrics

### Cost monitoring

- Track AI service costs in [AWS Cost Explorer](https://docs.aws.amazon.com/cost-management/latest/userguide/ce-what-is.html)
- Set up [billing alerts](https://docs.aws.amazon.com/cost-management/latest/userguide/budgets-managing-costs.html) for unexpected usage

### Operational monitoring

- Use [AWS CloudTrail](https://docs.aws.amazon.com/cloudtrail/) for API call auditing
- Implement [AWS Config](https://docs.aws.amazon.com/config/) for compliance monitoring
- Monitor model performance and drift using [SageMaker Model Monitor](https://docs.aws.amazon.com/sagemaker/latest/dg/model-monitor.html)

!!! tip "AI service optimization"

    - Use [AWS Trusted Advisor](https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor.html) to identify cost optimization opportunities
    - Implement caching strategies to reduce API calls where appropriate
    - Consider using [Amazon ElastiCache](https://docs.aws.amazon.com/elasticache/) for frequently accessed AI results

## LZA compliance and governance

When deploying AI services in the AWS Landing Zone Accelerator:

### Security guardrails

- All AI services must comply with existing LZA security policies
- Data encryption at rest and in transit is mandatory
- Network isolation requirements apply to AI workloads

### Data governance

- Ensure data residency requirements are met (Canada Central region)
- Implement proper data classification and handling procedures
- Follow B.C. government data governance policies for AI applications

### Model governance

- Document model usage, limitations, and decision-making processes
- Implement version control for models and training data
- Establish processes for model validation and testing

!!! note "AI ethics and responsible use"

    Consider implementing responsible AI practices:

    - Regular bias testing and fairness assessments
    - Transparency in AI decision-making processes
    - Human oversight for critical decisions
    - Clear documentation of AI system capabilities and limitations

    For guidance, see [AWS AI Service Cards](https://aws.amazon.com/ai/responsible-ai/resources/) for transparency documentation.

## Getting started with AI in LZA

1. **Plan your architecture**: Review the [AWS Well-Architected Machine Learning Lens](https://docs.aws.amazon.com/wellarchitected/latest/machine-learning-lens/welcome.html)
2. **Start small**: Begin with pilot projects using available models (Titan or Mistral)
3. **Implement monitoring**: Set up comprehensive monitoring from day one
4. **Follow security best practices**: Ensure your AI services meet LZA security requirements
5. **Document everything**: Maintain clear documentation of models, data flows, and decision processes

For additional assistance with AI service implementation, contact the [Public Cloud Service Desk](https://citz-do.atlassian.net/servicedesk/customer/portal/3) or see our [support page](../../../welcome/support.md) for all available support options.
