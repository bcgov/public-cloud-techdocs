# IAM User Management Service

## Introduction

- This solution employs AWS services such as DynamoDB, Lambda, and IAM for automated IAM user management and secure access key rotation. The required services are deployed into project sets within the ECF automation layers upon creation.

## How to Create IAM Users

To create an IAM user:

1. Insert a new item into the DynamoDB table `BCGOV_IAM_USER_TABLE`.
2. Set the `UserName` attribute to the desired IAM username.
![table](/images/iam-user-service/table.png)
![create-user](/images/iam-user-service/create-user.png)

- A Lambda function will trigger to create the IAM user, generate an access key, and store it in the SSM Parameter Store. This function also runs hourly to rotate keys as needed and ensure DynamoDB table entries align with actual IAM account users, removing any discrepancies.
![iam-user](/images/iam-user-service/iam-users.png)

IAM user name constraints:

- When creating an AWS IAM user, the constraints for naming are as follows:
  - Length: The name must be between 1 and 64 characters long.
  - Characters: The name can include only the following characters:
    - Uppercase and lowercase letters (A–Z and a–z)
    - Numbers (0–9)
    - Plus (+), equal (=), comma (,), period (.), at (@), underscore (_), and hyphen (-) characters
- These constraints ensure that user names are compatible with AWS naming conventions and can be used across various AWS services without issues. It's important to adhere to these guidelines to avoid errors during user creation and when assigning permissions or attaching policies to the user.

## How does key-rotation work ?

- The Lambda function manages key rotation by monitoring the age of the keys. When the current key is older than 2 days, it's marked as pending_deletion key and a new current key is created. Once the pending_deletion key reaches 4 days old, it is deleted and replaced with a new current_key.

- The lambda function also updates the keys in the parameter store automatically

## How to get the keys

- The lambda function automatically stores the keys created for users in the SSM parameter store, Users can get the keys from the parameter store and setup automation.
- The name with which the parameter is created `/iam_users/<user-name>_keys`.
- The keys are stored in the parameter store in the below json structure

```json
{
  "pending_deletion": {
    "AccessKeyID": "Access_Key_ID_Pending_Deletion",
    "SecretAccessKey": "Secret_Access_Key_Pending_Deletion"
  },
  "current": {
    "AccessKeyID": "Access_Key_ID_Current",
    "SecretAccessKey": "Secret_Access_Key_Current"
  }
}
```

![parameter](/images/iam-user-service/parameter.png)

## Setup Automation to retrieve and use keys

- Users can implement a checker in their automation scripts to verify if they are using a 'pending_deletion' key. If so, the script should automatically rotate to the 'current' key fetched from the Parameter Store.
- This way one can stay ahead of the key rotation and always use the current keys

## Permission boundaries

- Permission boundaries prevent privilege escalation by setting the maximum permissions users can have, even if their IAM policies allow more extensive permissions. For instance, an `AdministratorAccess` policy constrained by a permission boundary that only allows `AmazonS3FullAccess` will limit the user to S3 actions.
- The lambda function automatically set's permission boundaries to the user's created as a security measure.

### Deleting an IAM User

- Remove the corresponding entry from the DynamoDB table. The Lambda function will trigger and delete the user and their access keys from IAM and the SSM Parameter Store.

## Best Practices

### Applying least-privilege permissions

- When you set permissions with IAM policies, grant only the permissions required to perform a task. You do this by defining the actions that can be taken on specific resources under specific conditions, also known as least-privilege permissions. You might start with broad permissions while you explore the permissions that are required for your workload or use case. As your use case matures, you can work to reduce the permissions that you grant to work toward least privilege.

### Deny access to AWS based on the source IP

- In AWS we can create identity-based policy that denies access to AWS actions in the account when the request comes from principals outside the specified IP range.

- Example policy that allows s3 actions only to a specific ip range

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:*",
            "Resource": "*"
        },
        {
            "Effect": "Deny",
            "Action": "*",
            "Resource": "*",
            "Condition": {
                "NotIpAddress": {
                    "aws:SourceIp": [
                        "192.0.2.0/24"
                    ]
                },
                "Bool": {
                    "aws:ViaAWSService": "false"
                }
            }
        }
    ]
}
```
