# Identity & Access Management (IAM)

<img src="images/iam/AWS-IAM.jpg" width="auto" height="auto">

### IAM: Users & Groups

- **<span style="color:#1E90FF">Identity and Access Management (IAM)</span>** - is a critical AWS service that provides fine-grained access control across all AWS resources (With `IAM`, you can create and manage AWS `users` and `groups`, and use `permissions` to allow or deny their access to AWS resources). It's a <span style="color:#1E90FF">global service</span>, meaning it operates consistently across all AWS regions.


<img src="images/iam/IAM-Dashboard.PNG" width="auto" height="auto">
<p>

- **<span style="color:#1E90FF">The Root Account -</span>** A Special Entity. When you first create an AWS account, a root account is generated automatically. This account has unrestricted access to all AWS services and resources within the account. Due to its powerful nature, it's highly recommended to avoid using the root account for daily tasks and never share its credentials. Instead, create individual users and assign them appropriate permissions.

- **<span style="color:#1E90FF">IAM Users -</span>** Individual Entities. IAM users are entities that represent individuals or applications in your organization. Each user is created under your AWS account and is given specific permissions to access AWS resources. These users have their credentials (passwords, access keys, etc.) and can be managed independently.

- **<span style="color:#1E90FF">IAM Groups -</span>** Organizing Users. IAM groups allow you to organize users and manage permissions collectively. A group is simply a collection of IAM users, and it’s an effective way to assign the same set of permissions to multiple users simultaneously. For instance, you might create a group for developers, assigning them the permissions they need to perform their tasks.

- Key Points about Groups and Users:

    - <span style="color:#1E90FF">Groups Only Contain Users :</span> IAM groups can only include `users` and `cannot contain other groups`. This ensures a straightforward and understandable permission management structure.
    - <span style="color:#1E90FF">Users Can Belong to Multiple Groups :</span> An IAM user `doesn't have to belong to a group`, but they can belong to `multiple groups`. This flexibility allows you to easily manage permissions by adding or removing users from groups as needed.

<img src="images/iam/IAM-Users-Groups.PNG" width="auto" height="auto">
<p>

> **⚠️ Note:**
> 
> An IAM Group is not an identity and cannot be identified as a principal in an IAM policy
> 
> Only users and services can assume a role (not groups)
>
> A new IAM user created using the AWS CLI or AWS API has no AWS credentials

### IAM: Permissions

<img src="images/iam/IAM-Permissions.PNG" width="auto" height="auto">
<p>

- <span style="color:#1E90FF">IAM Permissions</span> in AWS are the rules that define what actions users or groups can perform on specific resources. These permissions are managed through policies, which are JSON documents that specify the allowed or denied actions.

- **<span style="color:#1E90FF">Policies:</span>** The Building Blocks of Permissions
    > Policies are the core mechanism in IAM to manage permissions. They can be attached to individual users or groups of users. A policy document is written in JSON format and contains one or more statements that define the permissions. Each statement specifies the effect (either `Allow` or `Deny`), the action(s) that are permitted or denied, and the resources to which these actions apply.

- **<span style="color:#1E90FF">Applying the Principle of Least Privilege</span>**
    > In AWS, it's crucial to follow the principle of least privilege when assigning permissions. This principle means that you should grant users and groups the minimum level of access they need to perform their tasks—nothing more. By doing so, you reduce the risk of unauthorized access and potential security breaches.

Below is an example of a policy that grants specific read-only permissions across several AWS services

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2:Describe*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "elasticloadbalancing:Describe*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudwatch:ListMetrics",
                "cloudwatch:GetMetricStatistics",
                "cloudwatch:Describe*"
            ],
            "Resource": "*"
        }
    ]
}
```

- The `Version` specifies the version of the policy language.
- The `Statement` array includes multiple statements, each with an Effect, Action, and Resource.
- The `Effect` is set to Allow, meaning the specified actions are permitted.
- The `Action` defines the specific operations that are allowed, such as describing EC2 instances or listing CloudWatch metrics.
- The `Resource` is set to "*", meaning the actions apply to all resources.