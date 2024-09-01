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

### IAM Policies inheritance

<img src="images/iam/IAM-Policies-Inheritance.PNG" width="auto" height="auto">
<p>

- **Developers Group:** Alice, Bob, and Charles are part of the Developers group. This group has a policy attached to it that defines specific permissions.

- **Operations Group:** David and Edward are members of the Operations group, which has its own policy.

- **Audit Team:** Charles, who is also a part of the Developers group, belongs to the Audit Team. Similarly, David , who is also a part of the Operations group, belongs to the Audit Team. This team has a different set of permissions defined by another policy.

- **Inline Policies:** Fred has a unique case where he is not part of any group but instead has an inline policy directly attached to him.

> **⚠️ Key Points:**
>
> **Multi-Group Membership:** A user can belong to multiple groups, and thus, inherit permissions from multiple policies. This allows for flexibility in defining complex access controls.
>
> **Combining Permissions:** When a user is part of more than one group, their effective permissions are a combination of all the policies attached to those groups. This can be both an advantage, allowing for granular control, and a challenge, as it may lead to unintended permissions if not carefully managed.
>
> **Inline Policies:** These are policies `directly` attached to an individual user rather than a group. They are specific to the user and not shared or inherited by other users.
>
> **Least Privilege Principle:** Even with inheritance, it’s essential to apply the principle of least privilege—granting only the permissions necessary for a user to perform their tasks. Over-permissioning can occur if a user inherits too many permissions from multiple sources.


### IAM Policies Structure

When working with AWS Identity and Access Management (IAM), it's essential to understand the structure of IAM policies. These policies are the fundamental building blocks that define permissions and access controls within AWS

<img src="images/iam/IAM-Policies-Structure.PNG" width="auto" height="auto">
<p>

#### Components of an IAM Policy

An IAM policy is a JSON document that consists of various elements, each serving a specific purpose. Here’s a breakdown of the key components:

1. Version
    - **Definition:** Specifies the language version of the policy. It's crucial to always include the version element in your policies.
    - **Value:** `"2012-10-17"` is the most current version and should be used for all new policies.

2. Id (Optional)
    - **Definition:** An identifier for the policy. This element is optional but can be useful for managing and identifying policies, especially in environments with numerous policies.

3. Statement
    - **Definition:** The core element of a policy, containing one or more individual statements. Each statement defines a single permission or a set of permissions.
    - **Structure:** A policy can have multiple statements, each addressing different actions, resources, or conditions.

    - **Components of a Statement** : Each statement within a policy has its own set of components:

        -   **Sid (Optional)**
            - **Definition:** A unique identifier for the statement. This is optional but can be helpful when managing complex policies with multiple statements.

        -   **Effect**
            - **Definition:** Specifies whether the statement `allows` or `denies` access.
            - **Values:** 
                - `Allow` grants the specified permissions.
                - `Deny` explicitly blocks the specified permissions.
  
        -   **Principal**
            - **Definition:** Identifies the AWS account, user, or role to which the policy applies. This element is crucial when defining who the policy is for.

        -   **Action**
            - **Definition:** A list of actions that the policy allows or denies. Actions are operations that can be performed on AWS resources, such as `s3:GetObject` or `ec2:StartInstances`.

        -   **Resource**
            - **Definition:** Specifies the AWS resources to which the actions apply. This can include specific resources (e.g., an S3 bucket) or a wildcard (`*`) to apply to all resources.

        -   **6. Condition (Optional)**
            - **Definition:** Specifies the circumstances under which the policy is in effect. Conditions can be used to add granularity, such as allowing access only from a specific IP address range or during a certain time period.