# AWS General Info

---

* Файлы на AWS загружаются прямо с браузера, перед этим генерится signature с помощью которого это делается
* AWS SMTP SES credentials differ from AWS console creds. They need to be generated separately and are used to send emails.
* AWS creates default VPC for every region
* A VPC spreads across the whole region covering all AZ, there’s a subnet per AZ
* No subnet can connect to internet unless its parent VPC has an internet gateway (IGW) setup
* In AWS, when Subnet gets assigned a range of IPs via CIDR block, you always get 5 fewer as they reserved for internal usage (first 4 and last 1 are reserved)
* Some AWS services are regional, i.e. data from one AZ is available and gets copied automatically to another within the region without any setup needed. Examples - S3, Elastic IPs, SQS, SNS
* On AWS there are service limits. For example you cannot create more than 20 EC2 instances per region. This is done to avoid abuse. You can request to increase the limit if required. Some of the other limits are: 5 VPCs per region, 200 subnets per VPC, 200 route tables per VPC, 500 Security groups per VPC.
* When VPC created, it by default gets a default route table and default network ACL (access control list). Note that the default NACL allows all inbound and outbound traffic with no restrictions. But when you create a new NACL it will always restrict all inbound and outbound traffic, so new NACL always needs to be configured.
* In NACL the first rule that matches the traffic is always applied
* Network ACL can be considered as a “firewall” for your subnet(s).
* Each subnet must be associated with a route table, note that multiple subnets can be associated to the same route table, but a subnet as said previously - must get associated to a route table. Also a subnet can have only one route table. Route table consists of a number of route rules each of each includes some parameters and one of the most important ones are Destination and Target, Destination lists IP addresses where the request/response is supposed to go and Target sets what route table should do if IP address matched for a specific destination set, for example if Target is “local” that means direct the request/response to back as the destination IP is internal, if for e.g. Target is some IGW then route table directs the request/response to the specified IGW
* Before creating any instance a subnet needs to be created inside VPC, otherwise you cannot create an instance
* Each instance within VPC gets internal DNS hostname, if an instance gets external IP - then it also gets external DNS hostname.
* AWS public IPs are taken from AWS pool of IP addresses
* When instance is stopped, the public IP is released and when instance starts again then new IP is associated with it. So to retain the public IP address you need to use Elastic IP which are static
* VPC can have only one IGW, it scales automatically as required
* An IGW cannot be detached from a VPC if there are active EC2 or RDS instances in the VPC
* If instances from private subnet need an internet access then often NAT gateways are used. NAT gateway is created in a public subnet and the private subnet route table is updated to direct all external internet related traffic (0.0.0.0/0) to NAT gateway from the public subnet. NAT gateway normally gets an elastic IP associated with it. NAT gateway scales automatically
* Security groups in a VPC specify which traffic is allowed to or from an Amazon EC2 instance. Security groups can be used to set only allow rules and by default allow all outbound traffic from an instance it is set against and no inbound traffic at all. Security groups perform stateful filtering. Stateful means if you allow traffic flow in one direction you don’t need to add rule in the other direction for response to go back
* Unlike Security Groups - Network ACLs operate at the subnet level, not an instance level. Network ACLs  evaluate traffic entering and exiting a subnet rather than instance. Network ACLs can be used to set both allow and deny rules. Network ACLs do not filter traffic between instances in the same subnet. In addition, network ACLs perform stateless filtering which means you need to specify what traffic you allow in and what traffic you allow out.
* Multiple Security Groups can be applied to an instance. But, only one Network ACL can be associated to a subnet
* The traffic flows to/from AWS instances in the following order (from the below you can see that each subnet must have a Network ACL and must have a route table): 
    1. Request comes from internet to VPC router
    2. VPC router directs request to a route table
    3. Then request gets to Network ACL
    4. Then Subnet
    5. Then Instances via security group
* A subnet can be associated to only one Network ACL, but multiple subnets can be associated to the same Network ACL
* Separate VPCs should always be created for development, staging (UAT - user acceptance testing) and production
* Normally security groups used for whitelisting traffic and Network ACLs are used for blacklisting traffic
* Access control is managed via IAM, here you can create users, permissions, user groups, roles etc. Users can get permissions to access AWS services, perform actions etc, but if a number of users need the same set of access they can be grouped into Groups. The main difference between Group and Role: AWS service cannot be assigned to a group to have an access to another AWS service (e.g. API Gateway needs an access to trigger Lambda Function), so Roles are being used instead. Roles are similar to Groups but are used for granting access to AWS service rather than users.
* Access control to S3 is done via: bucket policies (bucket level) or ACL i.e. access control list (object level)
