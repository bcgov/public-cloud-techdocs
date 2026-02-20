# Calgary region in AWS Secure Environment Accelerator (SEA)

Last updated: **{{ git_revision_date_localized }}**

## Overview

The Calgary region (ca-west) is a new addition to AWS's global infrastructure, enhancing cloud capabilities within Canada. This document aims to provide an update about the integration of the Calgary region within our AWS Landing Zone managed through the Secure Environment Accelerator (SEA).

### Current status

The Calgary region (ca-west) is a new AWS region in Canada. By default, new regions, including Calgary, are disabled in our AWS environment and must be activated manually before deploying resources.

The AWS SEA development team is integrating the Calgary region into the accelerator code base. This requires changes and deploying new core infrastructure in the region.

Currently, not all AWS services needed for the Secure Environment Accelerator are available in the Calgary region. AWS is gradually rolling out these services. Although AWS has launched the Calgary region, it is not yet ready for full integration into the SEA.

## Deploying workloads in the Calgary region

### Present capability

Currently, the Calgary region is not enabled in the SEA, so workloads cannot be deployed there through our setup.

## Service availability in the Calgary region

### Progressive rollout

AWS is gradually introducing services in the Calgary region. Not all services are available yet, but the list is growing.

### Monitoring service availability

For the latest information on the services available in the Calgary region, please refer to the [AWS Regional Product Services List](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/?ca-rg-secfour-aws-ca-serv-list).
