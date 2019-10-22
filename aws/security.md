# VPN Connections

By default, instances that you launch into an Amazon VPC can't communicate with your own (remote) network.
You can enable access to your remote network from your VPC by attaching a virtual private gateway to the VPC, creating a custom route table, updating your security group rules and AWS managed VPN connection.
AWS Direct Connect does not encrypt traffic in connections between AWS VPC's and the On-premises network.

Direct Connect :

AWS Direct Connect is a network service that provides an alternative to using the Internet to connect customer's on premise sites to AWS.
Using AWS Direct Connect, data that would have previously been transported over the Internet can now be delivered through a private network connection between AWS and your datacenter or corporate network.


# IAM

A customer needs corporate IT governance and cost oversight for all AWS resources consumed by its devisions. Each divison has their own AWS account and there is a need to ensure that security policies are kept in place at the Account Level. How can you achieve this ?

Use AWS Organizations + Use service control policies

With AWS Organizations, you can centrally manage policies across multiple AWS accounts without having to use custom scripts and manual process. For example, you can apply service control policies (SCPs) across mulitple AWS accounts that are members of an organization. SCPs allow you to define which AWS service APIs can and cannot be executed by AWS identity and Access Management (IAM) entities (such as IAM users and roles) in your organization's member AWS accounts. SCPs are created and applied from the master account, which is the AWS account that you used when you created your organization.

# Web Applications

- For DDoS attacks use a CDN service in front of the web application
- For Cross site scripting and others use AWS WAF.
AWS WAF is a web application firewall that helps protect your web applications from common web exploits that could affect application availability, compromise security, or consume excessive ressources.

# AWS Config  

- AWS Config is a service that enables you to assess, audit, and evaluate the configurations of your AWS resources.
- Config continuously monitors and records your AWS resource configurations and allows you to automate the evaluation of recorded configurations against desired configurations.
- With Config, you can review changes in configurations and relationships between AWS resources, dive into detailed resource configuration histories, and determine your overall compliance against the configurations specified in your internal guidelines. This enables you to simplify compliance auditing, security analysis, change management, and operational troubleshooting.

# When to create multiple AWS accounts

- Most companies will want to create more than one AWS account because multiple accounts provide the highest level of resource and security isolation.
- Answering "yes" to any of the following questions is a good indication that you should consider creating additional AWS accounts :
  - Does the business require administrative isolation between workloads ?
  - Does the business require limited visibility and discoverability of workloads ?
  - Does the business require isolation to minimize the blast radius ?
  - Does the business require strong isolation of recovery and/or auditing data?

# Amazon Cognito

- Provides authentification, authorization and user management for your web and mobile apps.
- Your users can sign in directly with a user name and password, or through a third party such as Facebook, Amazon or Google.
- The two main components of Amazon Cognito are user pools and identity pools.
- User Pools :
  - provide sign-up and sign-in options for your app users.
  - Social sign in : facebbok, google, Amazon..
  - User directory Management
  - Security features such as MFA
  - Built in customizable web UI to sign in Users

- Identity Pools :
  - enables you to grant your users access to other AWS services.
  - enables users to obtain temporary AWS credentials to access AWS services.  

- You can use User Pools and Identity Pools, together or separated.
- Availability : Cognito is distributed across multiples AZ's  


# PCI Compliance if web app is using AWS CloudFront service

- AWS recommend that you log your CloudFront usage data for the last 365 days for future auditing purposes. To log usage data you can do the following :
    - Enable CloudFront access logs
    - Capture requests that are sent to the CloudFront API

# NAT Gateway High Availability :

- if you have resources in multiple AZ's and they share one NAT Gateway, in the event that the NAT Gateway's Availability Zone is down, resources in the other AZ lose internet access.
- To create an AZ-independent architecture, create a NAT Gateway in each AZ and configure your routing to ensure that resources use the NAT Gateway in the same AZ. 
