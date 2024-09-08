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

When working with AWS Identity and Access Management (IAM), it's essential to understand the structure of IAM policies. These policies are the fundamental building blocks that define permissions and access controls within AWS.

- Policies are JSON documents that outline permissions for users, groups or roles

- Two types
    - **User based policies**
        - IAM policies define which API calls should be allowed for a specific user
    - **Resource based policies*
        - Control access to an AWS resource
        - Grant the specified principal permission to perform actions on the resource and define under what conditions this applies

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

        - **Sid (Optional)**
            - **Definition:** A unique identifier for the statement. This is optional but can be helpful when managing complex policies with multiple statements.

        - **Effect**
            - **Definition:** Specifies whether the statement `allows` or `denies` access.
            - **Values:** 
                - `Allow` grants the specified permissions.
                - `Deny` explicitly blocks the specified permissions.
        - **Principal**
            - **Definition:** Identifies the AWS account, user, or role to which the policy applies. This element is crucial when defining who the policy is for.
        - **Action**
            - **Definition:** A list of actions that the policy allows or denies. Actions are operations that can be performed on AWS resources, such as `s3:GetObject` or `ec2:StartInstances`.
        - **Resource**
            - **Definition:** Specifies the AWS resources to which the actions apply. This can include specific resources (e.g., an S3 bucket) or a wildcard (`*`) to apply to all resources.
        - **6. Condition (Optional)**
            - **Definition:** Specifies the circumstances under which the policy is in effect. Conditions can be used to add granularity, such as allowing access only from a specific IP address range or during a certain time period.

### IAM – Password Policy:

- Strong passwords = higher security for your account
- In AWS, you can setup a password policy:
    - Set a minimum password length
    - Require specific character types:
        - including uppercase letters
        - lowercase letters
        - numbers
        - non-alphanumeric characters
- Allow all IAM users to change their own passwords
- Require users to change their password after some time (password expiration)
- Prevent password re-use

<img src="images/iam/IAM-Edit-Password-Policy.PNG" width="auto" height="auto">
<p>

#### Multi Factor Authentication - MFA

- Users have access to your account and can possibly change
configurations or delete resources in your AWS account
- **You want to protect your Root Accounts and IAM users**
- MFA = password you know + security device you own

<img src="images/iam/IAM-MFA.PNG" width="auto" height="auto">
<p>

- **Main benefit of MFA:** if a password is stolen or hacked, the account is not compromised

#### MFA devices options in AWS

Multi-Factor Authentication (MFA) adds an additional layer of security to your AWS account by requiring a second form of authentication, typically something you have, like a device, in addition to your password. There are various types of MFA devices you can use, such as:

- **Virtual MFA Devices:** Applications like Google Authenticator and Authy, which generate time-based one-time passwords (TOTP) on your phone.

- **Hardware MFA Devices:** Physical devices like YubiKey, which can be used for multiple IAM users, or other key fobs that generate time-based one-time passwords.

<img src="images/iam/IAM-MFA-Device-1.PNG" width="auto" height="auto">
<p>

<img src="images/iam/IAM-MFA-Device-2.PNG" width="auto" height="auto">
<p>

These devices enhance the security of your AWS account by ensuring that even if your password is compromised, the attacker would still need the second factor to gain access.

### How can users access AWS ?

- To access AWS, you have three options:
    1. **AWS Management Console:** This is a web-based interface that allows users to manage AWS services visually. It is secured by a combination of a password and Multi-Factor Authentication (MFA), ensuring an additional layer of security beyond just a password.
    2. **AWS Command Line Interface (CLI):** The CLI allows users to interact with AWS services via command-line commands. This is particularly useful for automation and managing AWS resources programmatically. Access to the CLI is protected by Access Keys, which consist of an Access Key ID and a Secret Access Key. These keys are unique to each user and act as credentials that grant permission to access AWS services. The CLI is popular among developers and system administrators who need to perform complex tasks or automate workflows.
    3. **AWS Software Development Kit (SDK):** The SDK is designed for integrating AWS services directly into your applications through code. It provides APIs that allow developers to interact with AWS services in a variety of programming languages, including Java, Python, and JavaScript. Like the CLI, the SDK requires Access Keys for authentication. These keys are managed by users and should be treated with the same level of security as a password, as they grant programmatic access to AWS resources.

#### Access Keys:
- Access Keys are generated through the AWS Console
- Users manage their own access keys
- **Users manage their own access keys**
- Access Key ID ~= username
- Secret Access Key ~= password

<img src="images/iam/IAM-Fake-Access-key.png" width="auto" height="auto">
<p>

- Access key ID: AKIASK4E37PV4983d6C
- Secret Access Key: AZPN3zojWozWCndIjhB0Unh8239a1bzbzO5fqqkZq
- **Remember: don’t share your access keys**

#### What’s the AWS CLI?

- A tool that enables you to interact with AWS services using commands in your command-line shell
- Direct access to the public APIs of AWS services
- You can develop scripts to manage your resources
- It’s open-source https://github.com/aws/aws-cli
- Alternative to using AWS Management Console
- Setup CLI https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

<img src="images/iam/IAM-CLI.png" width="auto" height="auto">
<p>

#### What’s the AWS SDK?

- AWS Software Development Kit (AWS SDK)
- Language-specific APIs (set of libraries)
- Enables you to access and manage AWS services programmatically
- Embedded within your application
- Supports
    - SDKs (JavaScript, Python, PHP, .NET, Ruby, Java, Go, Node.js, C++)
    - Mobile SDKs (Android, iOS, …)
    - IoT Device SDKs (Embedded C, Arduino, …)
- Example: AWS CLI is built on AWS SDK for Python

<img src="images/iam/IAM-SDK.png" width="500" height="500">
<p>

#### AWS CloudShell

AWS CloudShell is a browser-based shell environment available directly in the AWS Management Console. It provides a convenient way to securely manage, interact with, and automate AWS resources from your browser. Here are the key points:

- **Pre-installed Tools:** `AWS CLI`, `Git`, and other essential tools are pre-installed, so you can start working immediately without any setup.
- **Persistent Storage:** `1 GB` of persistent storage is available for your home directory, allowing you to store scripts and other files.
- **Security:** Access to resources is securely managed via your IAM credentials, ensuring secure operations.
- Avalibility AWS Regions for AWS CloudShell https://docs.aws.amazon.com/cloudshell/latest/userguide/supported-aws-regions.html

AWS CloudShell is ideal for users who need a quick and easy way to manage AWS services without setting up a local environment.

### IAM Roles for Services

- Some AWS service will need to perform actions on your behalf <br>
- To do so, we will assign <b>permissions</b> to AWS services with <b>IAM Roles</b><br>
- IAM roles provide permissions to entities you trust: AWS services, users, or applications. <br>
- Roles are designed to be assumed by anyone or anything that requires them, unlike users who are tied to a specific individual.<br>
- Common roles:<br>
    - EC2 Instance Roles<br>
    - Lambda Function Roles<br>
    - Roles for CloudFormation<br>

<img src="images/iam/IAM-Role.PNG" width="500" height="500">

- Steps to Create a Custom IAM Role
    - Navigate to IAM in AWS Console: Go to the IAM section in your AWS Management Console.
    - **Create a Role:**
        - Click on `Roles` in the navigation pane and select `Create role`.
        - Choose the type of trusted entity (e.g., AWS service, another AWS account, web identity, etc.).
    - **Select Use Case:** Depending on the trusted entity, select the specific use case, like allowing S3 access for an EC2 instance.

    **Trusted entities**
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Principal": {
                    "Service": "ec2.amazonaws.com"
                },
                "Action": "sts:AssumeRole"
            }
        ]
    }
    ```
    - **Attach Permissions:** Choose or create a policy that specifies the permissions this role will have. You can either attach an existing policy or create a new one.
    - **Set Trust Relationship:** Define which entities can assume this role by setting up a trust policy.
    - **Name the Role:** Give your role a descriptive name and add an optional description.
    - **Review and Create:** Review the settings and create the role.


For a detailed walkthrough on creating a custom IAM role, you can watch this [YouTube tutorial](https://www.youtube.com/watch?v=2eu0YqbaOyo&t=130s)


<img src="images/iam/IAM-Role-Permissions.png" width="auto" height="auto">
<p>

> **⚠️ Trust Policies:**
>
> Defines which principal entities (accounts, users, roles, federated users) can assume the role
>
> An IAM role is both an `identity` and a `resource` that supports `resource-based policies`.
>
> You must attach both a `trust policy` and an `identity-based` policy to an IAM role.
>
> The `IAM service supports only one type of resource-based policy` called a `role trust policy`, which is attached to an `IAM role`.

> **⚠️ Roles:**
>
> Collection of policies for AWS services
> 
> If you are going to use an `IAM Service Role` with `Amazon EC2` or another AWS service that uses `Amazon EC2`, you must store the role in an `instance profile`. When you create an `IAM service role for EC2`, the role automatically has EC2 identified as a trusted entity.

### IAM Assume Role

In the AWS Identity and Access Management (IAM) ecosystem, one of the most powerful features is the ability to `assume a role`. This capability allows a user or a service to temporarily adopt permissions assigned to a different IAM role, which can significantly enhance security and flexibility within your AWS infrastructure.

IAM roles are similar to IAM users in that they both define permissions for actions within your AWS environment. However, IAM roles are designed to be assumed by trusted entities, including users, applications, or services, rather than having permanent permissions like a typical user.

For example, you might have multiple users within an AWS account that need temporary access to S3 resources. Instead of assigning S3 permissions to each user, you can create a role that grants S3 access and allow users to assume that role when needed.

#### The Core Concept of Assume Role

When a user or an AWS service assumes a role, they temporarily gain the permissions associated with that role. This enables flexible and temporary access control without needing to grant specific permissions directly to the user or service.

<img src="images/iam/IAM-Assume-Role.png" width="auto" height="auto">
<p>

##### How Does It Work?

1. **Creating the IAM Role**: 
   - First, create a role with the necessary permissions. For example, you can create an IAM role that grants full access to S3.
   - Assign an appropriate policy to the role. In our case, this could be an S3 Full Access policy.

2. **Assume Role Mechanism**:
   - The role and the user are initially independent; no permissions are assigned to the user directly.
   - The user can assume the role by requesting to switch to it. Once the user assumes the role, they inherit the permissions defined by the role’s policy, allowing them to perform actions such as accessing S3.

3. **Example**:
   - Let’s consider a test user who doesn’t have any permissions assigned directly. 
   - After creating an IAM role with S3 Full Access, we allow the test user to assume this role.
   - Upon assuming the role, the test user will be able to perform S3 operations like listing, creating, or deleting buckets.

##### Step-by-Step Guide to Assume a Role in AWS

###### Step 1: Create a Test User
- In the IAM console, create a user without assigning any permissions or groups.
- This user will initially have no access to any AWS resources.

###### Step 2: Create the IAM Role
- Navigate to IAM and create a new role.
- Attach the S3 Full Access policy to this role. This policy allows access to all S3-related operations.
- Ensure that the role can be assumed by the account where the user exists.

###### Step 3: Attach Role to User
- In the IAM console, go to the user management section.
- Create an inline policy that allows the test user to assume the S3 Full Access role using the `sts:AssumeRole` action.
- Specify the ARN of the role in the policy.

###### Step 4: Assume the Role
- Using the AWS Management Console, the test user can now switch roles by following the URL generated for that role.
- Once the role is assumed, the user will have temporary S3 access, and they can perform operations like listing and creating S3 buckets.

###### Step 5: Switching Back
- After completing the necessary operations, the user can switch back to their original role by logging out of the assumed role. This restores the user’s permissions to the original state, without the S3 access.


##### Security Benefits of Assume Role

The `assume role` feature enhances security by limiting access to sensitive resources. Instead of providing long-term access to resources (which could be misused or forgotten), you can use roles to grant temporary, fine-grained access. For example:

- **Cross-Account Access**: You can use the assume role feature to grant permissions to users or services in different AWS accounts, enabling secure access without creating permanent trust relationships.
- **Task-Specific Access**: Rather than giving broad permissions to users or services, you can create roles tailored to specific tasks or functions, ensuring that only the required permissions are granted.

For a detailed walkthrough on creating a Assume IAM role, you can watch this [YouTube tutorial](https://www.youtube.com/watch?v=sFoMyPOPrSM&list=PL7iMyoQPMtAPVSnMZOpptxGoPqwK1piC6&index=7)


### IAM Security Tools

#### IAM Credentials Report (account-level)

The IAM Credential Report is a valuable security tool provided by AWS that helps administrators audit the credentials of IAM users in their AWS environment. The report is a `CSV` file that contains comprehensive information about the status of passwords, access keys, and multi-factor authentication (MFA) for all IAM users in the account.

##### Key Features:
- **Password and Access Key Information:** It shows when each user’s password and access keys were last used, or if they have never been used. This helps identify inactive or unused credentials that may pose a security risk.
- **MFA Status:** The report indicates whether MFA is enabled for each user. Enforcing MFA is crucial for enhancing account security by requiring a second layer of authentication.
- **Key Rotation:** The report helps track when access keys were last rotated, which is a key practice in maintaining secure access to AWS resources.

##### Use Cases:
- **Security Audits:** The credential report helps administrators review user access and ensure credentials are up-to-date and secure.
- **Compliance Monitoring:** It’s useful for ensuring that security policies, like MFA enforcement and key rotation, are being followed.
- **Inactive Users:** You can quickly identify accounts with credentials that haven’t been used in a long time, allowing you to disable or delete them to reduce the attack surface.

##### Best Practices:
- **Regular Review:** Generate and review the credential report regularly to stay on top of potential security gaps.
- **Enforce MFA:** Ensure all users, especially those with console access, have MFA enabled.
- **Rotate Access Keys:** Encourage regular key rotation and disable inactive or unused access keys to minimize security risks.

#### IAM Access Advisor (user-level)

IAM Access Advisor is a tool that provides detailed insights into how IAM roles and users are using their permissions. It shows when each service was last accessed by a role or user, helping administrators assess whether the assigned permissions are necessary or overly broad.

<img src="images/iam/IAM-Access-Advisor.PNG" width="auto" height="auto">
<p>

##### Key Features:
- **Service Access Insights:** It displays the last accessed time for AWS services by each role or user. This can help identify permissions that are assigned but not being used.
- **Policy Optimization:** If a user or role hasn’t accessed a particular service in a while, you can consider removing those permissions to adhere to the principle of least privilege.
- **Cross-Account Access:** It shows which services are accessed across accounts, helping in monitoring and securing cross-account permissions.
##### Use Cases:
- **Rightsizing Permissions:** Access Advisor is a great tool for determining if users or roles have permissions they don’t need, allowing you to reduce unnecessary access.
- **Auditing and Security Reviews:** It provides valuable information during security audits and helps ensure that permissions granted to users are actually being used.
- **Policy Cleanup:** Use Access Advisor data to remove unused or unnecessary permissions, keeping your policies minimal and focused.
##### Best Practices:
- **Regular Permission Reviews:** Regularly use Access Advisor to review the permissions assigned to your users and roles, removing unnecessary or overly broad access.
- **Least Privilege Enforcement:** Use Access Advisor’s insights to align with the principle of least privilege, ensuring users and roles only have access to what they need.
- **Monitor Service Access:** Keep an eye on cross-account access to ensure that only the necessary services are accessed between accounts.


### IAM Guidelines & Best Practices

- **Limit Root Account Usage**: Only use the root account for administrative tasks that cannot be performed by any other user.
- **One AWS User per Person**: Ensure each individual has their own unique AWS user account.
- **Use Groups for Permissions**: Assign users to groups and manage permissions at the group level for easier access control.
- **Enforce Strong Password Policies**: Implement a robust password policy to enhance security.
- **Enable Multi-Factor Authentication (MFA)**: Require MFA for all users to add an extra layer of protection.
- **Leverage IAM Roles for Permissions**: Use IAM roles to grant permissions instead of assigning them directly to users.
- **Programmatic Access (CLI / SDK) via Access Keys**: Use access keys for programmatic access, but avoid using them for console logins.
- **Regularly Audit Permissions**: Audit permissions of your account using IAM Credentials Report & IAM Access Advisor
- **Never Share Credentials**: Avoid sharing IAM user credentials and access keys between individuals.

### IAM Section – Summary
- Users: mapped to a physical user, has a password for AWS Console
- Groups: contains users only
- Policies: JSON document that outlines permissions for users or groups
- Roles: for EC2 instances or AWS services
- Security: MFA + Password Policy
- AWS CLI: manage your AWS services using the command-line
- AWS SDK: manage your AWS services using a programming language
- Access Keys: access AWS using the CLI or SDK
- Audit: IAM Credential Reports & IAM Access Advisor

<a href="README.md">
  <img src="images/Home.PNG" alt="Home" width="30" height="30">
</a>
