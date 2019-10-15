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
