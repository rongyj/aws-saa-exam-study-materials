# EC2

* [EC2 FAQs](https://aws.amazon.com/ec2/faqs/)

- 101
    - EC2: Elastic Compute Cloud
    - A web service that provides resizable compute capacity in the cloud. EC2 reduces the time required to obtain and boot new server instances to minutes, allowing you to quickly scale capacity, both up and down, as your computing requirements change.
    - Pricing models:
        - On demand - Allows you to pay a fixed rate by the hour (or the second) with no commitment.
            - Useful for those who want the low cost and flexibility of Amazon EC2 without any up-front payment or long-term commitment.
            - Applications with short term, spiky, or unpredictable workloads that *cannot* be interrupted.
            - Applications being developed or tested on EC2 for the first time.
        - Reserved - Provides you with a capacity reservation, and offer a significant discount on the hourly charge for an instance. Contract Terms are 1 and 3 year.
            - Useful for applications with steady state or predictable usage.
            - Applications that require reserved capacity.
            - Users able to make upfront payments to reduce their total computing costs even further.
            - Reserved Pricing Types:
                - Standard Reserved Instances - up to 75% off on-demand instances depending on contract length. Locked into type of EC2 instance.
                - Convertible Reserved Instances - Up to 54% off on-demand pricing. Has the capability to change the RI (reserved instance) type if workload requires different compute needs. This is as long as the exchange results in the creation of RIs of equal or greater value.
                - Scheduled Reserved Instances - These are available to launch within pre-determined time windows.
        - Spot - Enabled you to bid whatever price you want for instance capacity, providing for even greater savings if your application have flexible start and end times.
            - Useful for applications that have a flexible start and end times.
            - Applications that are only feasible at very low compute prices.
            - Users with urgent computing needs for large amounts of additional capacity.
        - Dedicated Hosts - Physical EC2 server dedicated for your use. Dedicated Hosts can help you reduce costs by allowing you to use your existing server-bound software licenses.
            - Useful for regulatory requirements that not support multi-tenant virtualization.
            - Great for licensing which does not support multi-tenancy or cloud deployments.
            - Can be purchased on-demand (hourly).
            - Can be purchased as a reservation for up to 70% off the on-demand price.
    - Instance types (not a part of associate exam, but good to know, and a part of the professional exam).

    - Exam Tips:
        - What EC2 is and how it helps save time and money
        - Pricing types
        - If spot instances are terminated by Amazon EC2, you will not be charged for a partial hour of usage. If you terminate the instance yourself, you will be charged for the hour.
            - This is a popular exam question.
- Lab 1

    Creating our first EC2 instance

    - Note: The availability zones in subnet dropdown are randomized, so us-east-1a in account 1, may not actually be us-east-1a in account 2)
    - Clicking add new volume on add storage page shows more volume types because they only want the root volume to run on an SSD or a HDD
- Lab 2
    - You can encrypt a root volume on an EC2 instance from the start now
    - Delete on termination flag lets you decide if you're going to delete the volume on termination of the instance
    - Termination protection is off by default
    - Default action is to delete root ebs volume on termination, but additional volumes won't be by default
    - Additional volumes can be encrypted
- Security Groups
    - Security Group Rule changes take effect immediately
    - Security Groups are stateful
        - if you create an inbound rule, an outbound rule is also created
            - if you allow http ingress, it will also be allowed back out
    - Individual port/IPs blacklisting is not doable from a security group
    - By default security groups have no permissions, things have to be added — least privilege
    - You can assign multiple SGs to an EC2 instance
        - demonstrate by adding rules to default and add default to an instance
    - Exam Tips:
        - All inbound traffic is blocked by default
        - All outbound traffic is allowed
        - Changes to SGs take effect immediately
        - EC2 to SGs is a many to many relationship
        - Security groups are stateful
            - if you enable inbound on a port, outbound is allowed on that port
        - You cannot block specific IP addresses using security groups, instead you need to use NACLs (more on this later)
        - You can set allow rules, but not deny rules in SGs
            - because by default they deny everything, you add rules to allow
- EBS 101
    - What is EBS
        - Elastic Block Store
            - A virtual hard disk in the cloud that provides persistent block storage volumes for use with Amazon EC2 instances in the AWS cloud. Each Amazon EBS volume is automatically replicated within its AZ to protext you from component failure, offering high availability and durability.
    - 5 Types of EBS:

        ![EC2%209123fe7313ca41209c69342e0ae1a60e/Screen_Shot_2020-05-06_at_9.01.52_PM.png](EC2%209123fe7313ca41209c69342e0ae1a60e/Screen_Shot_2020-05-06_at_9.01.52_PM.png)

        - should memorize API names of volume types
- Volumes and Snapshots
    - Volumes and EC2 instances will be in the same availability zone
    - You can change availability zones on the fly
    - You can take a snapshot of a volume and use this image as a your AMI for a new instance. This allows you to take your image and deploy an instance in a new AZ. You can even copy that AMI into a different region entirely to stand up new instances in a different region.
    - Snapshots exist on S3
    - Snapshots are point-in-time volumes
    - Snapshots are incremental
        - only changed blocks are logged since your last snapshot (think time machine)
    - To create a snap for Amazon EBS that will serve as root, stop the instance before taking the snapshot
    - You can create AMIs from snaps
    - You can change EBS volume sizes on the fly, including changing the size and storage type
- AMI Types
    - EBS
    - Instance Store
    - You can select your AMI based on:
        - Region
        - OS
        - Architecture
        - Launch Permissions
        - Storage for the Root Device
            - Instance Store (Ephemeral Storage)
            - EBS Backed Volumes
    - All AMIs are categorized as either backed by EBS or backed by Instance Store
        - For EBS Volumes: The root device for an instance launched from the AMI is an Amazon EBS snapshot.
        - For Instance Store Volumes: The root device for an instance launched from the AMI is an instance store volume created from a template stored in Amazon S3.
            - Instance store root volume types are of Instance Store.
            - Additional volumes have to be added at start up
            - Instance Store volumes ado not show up in the Volumes menu of the EC2 Dashboard
            - You can only reboot or terminate instance store backed EC2 instances
            - If the underlying hypervisor stops, you lose all your data (hence ephemeral)
    - Exam Tips:
        - Instance Store Volumes are called Ephemeral Storage
        - Instance Store Volumes cannot be stopped. If the underlying host fails, you will lose your data.
        - EBS backed instances can be stopped. You will not lose the data on this instance if it is stopped.
        - You can reboot both without losing your data.
        - By default, both ROOT volumes will be deleted on termination. However, with EBS volumes, you can tell AWS to keep the root device volume.
- ENI vs ENA vs EFA
    - Elastic Network Interfaces (ENI) — essentially a virtual network card
        - Auto attached to an EC2 instance, but you can add more
        - Allows:
            - A primary private IPv4 address from the IPv4 address range of your VPC
            - One or more secondary private IPv4 addresses from the IPv4 address range of your VPC
            - One Elastic IP address (IPv4) per private IPv4 address
            - One Public IPv4 address
            - One or more IPv6 addresses
            - One or more security groups
            - A MAC address
            - A source/destination check flag
            - A description
        - When would you use one:
            - Create a management network - separate to prod network
            - Use network and security appliances in your VPC
            - Create dual-homed instances with workloads/roles on distinct subnets
            - Create a low-budget, high-availability solution
        - Note: adding more ENIs doesn't necessarily increase your network throughput
    - Enhanced Networking (EN) — Uses single root I/O virtualization (SR-IOV) to provide high-performance networking capabilities on supported instance types.
        - SR-IOV is a method of device virtualization that provides higher I/O performance and lower CPU utilization compared to traditional virtualized network interfaces.
        - EN provides higher bandwidth, higher packet per second (PPS) performance, and consistently lower inter-instance latencies. There is no additional charge for using EN.
        - Use where you want good network performance.
        - Depending on instance type, EN can be enabled using two different methodologies:
            - Elastic Network Adapter (ENA) which supports network speeds of up to 100 Gbps
            - Intel 82599 Virtual Function interface, which supports network speeds of up to 10 Gbps. This is typically used on older instances.
            - (In any scenario question, you probably want to choose ENA over VF if given the option)
    - Elastic Fabric Adapter — A network device that you can attach to your EC2 instance to accelerate High Performance Computing and Machine Learning applications.
        - Provides a lower and more consistent latency and higher throughput than the TCP transport traditionally used in cloud-based HPC systems.
        - EFA can use OS-Bypass, which enables HPC and machine learning applications to bypass the operating system kernel and communicate directly with the EFA device. It makes it a lot fast with a lot lower latency. Currently only supported on Linux.
    - Exam Tips:
        - ENI - For basic networking at low cost. Separate management or logging network — use an ENI for each network.
        - EN - For when you need speeds between 10Gbps and 100Gbps
        - EFA - For when you need HPC/ML/OS-Bypass
- Encrypted Root Device Volumes & Snapshots
    - Root device is just the EBS volume that has your OS on it
    - Root devices can be provisioned encrypted immediately
    - Process for encrypting
        - Create a snapshot of the unencrypted root device volume
        - Create a copy of the snapshot and select the encryption option
        - Create an AMI from the encrypted snapshot
        - Use that AMI to launch new encrypted instances
    - Exam Tips:
        - Snapshots of encrypted volumes are encrypted automatically
        - Volumes restored from encrypted snapshots are encrypted automatically
        - You can share snapshots, but only if they are unencrypted
        - These snapshots can be shared with other AWS accounts or made public
        - You can now encrypt root device volumes upon creation of the EC2 instance
- CloudWatch 101
    - Comes up quite a bit in the exam
    - What is CloudWatch?
        - CloudWatch is a monitoring service to monitor your AWS resources, as well as the applications that you run on AWS
        - Can monitor things like:
            - Compute:
                - EC2 instance
                - Autoscaling groups
                - ELBs
                - Route53 Health Checks
            - Storage & Content Delivery
                - EBS Volumes
                - Storage Gateways
                - CloudFront
        - Host Level metrics
            - CPU
            - Network
            - Disk
            - Status Checks
    - AWS will try to confuse you between cloudwatch and cloudtrail:
        - AWS CloudTrail increases visibility into your user and resource activity by recording AWS Console actions and API calls. You can identify which users and accounts called AWS, the source IP address from which the calls were made, and when the calls occurred.
        - Think of a CCTV for cloudtrail C(CT)V
    - Exam Tips:
        - CloudWatch is used for monitoring performance
        - CloudWatch can monitor most of AWS as well as your applications that run on AWS
        - CloudWatch with EC2 will monitor events every 5 minutes by default.
        - You can have 1 minute intervals by turning on detailed monitoring
        - You can create CloudWatch alarms which trigger notifications.
        - CloudWatch is all about performance. CloudTrail is all about auditing (think audit trail → cloudtrail)
            - Scenario: who is provisioning what? — CloudTrail
            - Scenario: what is network throughput or disk i/o on ec2 instance? — CloudWatch
- CloudWatch Lab
    - Exam Tips:
        - Standard monitoring is every 5 minutes, detailed is every one minutes.
        - Dashboards - Create dashboards to see what is happening with your AWS environment
        - Alarms - Set alarms that notify you when particular thresholds are hit
        - Events - CloudWatch Events help you respond to state changes in your AWS resources
        - Logs - CloudWatch Logs help you to aggregate, monitor, and store logs
- The AWS Command Line
    - You can interact with AWS from anywhere in the world just using the CLI
    - You need to set up access in IAM
    - Commands are not in exam, but some basic commands will be useful to know for real life
- Using IAM Roles with EC2
    - Exam Tips:
        - Roels are more secure than storing your access key and secret access key on individual EC2 instances
        - Roles are easier to manage
        - Roles can be assigned to an EC2 instance after it is created using both the console and the CLI
        - Roles are universal — you can use them in any region
- Using Boot Strap Scripts
    - Using the advanced settings section of the console, you can drop in bash scripts to bootstrap server set up
    - If you need to access other AWS resources, the role attached to the EC2 instance will need to have access to those resources
- EC2 Instance Meta Data
    - if you curl [http://169.254.169.254/latest/](http://169.254.169.254/latest/) with various paths, you will receive the meta-data associated with that path
    - Bootstrap script is available at /user-data
    - Metadata is at /meta-data
    - Local IPv4 address at /meta-data/local-ipv4
    - Public IPv4 address at /meta-data/public-ipv4
    - Exam Tips:
        - meta-data used to get information abotu an instance
        - can also get user data (bootstrap script that runs when instance is provisioned
- Elastic File System
    - What is Elastic File System (EFS) ?
        - EFS is a file storage service for Amazon EC2 instances. EFS is easy to use and provides a simple interface that allows your to create and configure file systems quickly and easily. With EFS, storage capacity is elastic, growing and shrinking automatically as you add and remove files, so your applications have the storage they need, when they need it.
        - Multiple EC2 instances cannot share an EBS volume, but they can share an EFS volume
        - No need to pre-provision storage
    - EFS has lifecycle policies as does S3
        - Items can be moved to EFS IA after 14, 30, 60, or 90 days
    - Exam Tips:
        - Support the NFSv4 protocol
        - You only pay for storage used
        - Can scale up to PB
        - can support thousands of concurrent NFS connections
        - Data is stored across multiple AZs within a region
        - Read After Write Consistency
        - No need to duplicate files across EC2 instances
        - Linux only connectivity — Windows uses SMB
- FSX for Windows and FSX for Lustre
    - Amazon FSx for Windows File Server provides a fully managed native Microsoft Windows file system so you can easily move your Windows-based applications that require file storage to AWS. Amazon FSx is built on Windows Server.
        - How is different from EFS?
            - A managed windows server that runs windows server message block (SMB)-based files services.
            - Designed for Windows and Windows Applications.
            - Supports AD users, access control lists, groups and security policies, along with Distributed Files System (DFS) namespaces and replication.
        - Amazon FSx for Lustre is a fully managed file system that is optimized for compute-intensive workloads, such as high-performance computing, machine learning, media data processing workflows, and electronic design automation.
            - With Amazon FSx, you can launch and run a Lustre file system that can process massive data sets at up to hundreds of gigabytes per second of throughput, millions of IOPS, and sub-millisecond latencies.
            - How is Lustre FSx different from EFS?
                - Designed specifically for fast processing of workloads like ML, HPC, video processing, financial modeling, and electronic design automation
                - Lets you launch and run a file system that provides sub-millisecond access to your data and allows you to read and write data at speeds of up to hundres of gigabytes per second of throughput and millions of IOPS.
        - Exam Tips:
            - You'll get scenario based questions on when to use FSx for Windows, FSx for Lustre, or EFS:
                - EFS — When you need distributed, highly resilient storage for Linux instances and Linux-based applications.
                - FSx for Lustre — When you need high-speed, high-capacity distributed storage. This will be for applications that do HPS, financial modeling, etc. Remember that FSx for Lustre can store data directly on S3.
                - FSx for Windows — When you need centralized storage for Windows-based applications such as Sharepoint, Microsoft SQL Server, Workspaces, IIS Web Server or any other native Microsoft Application.
- EC2 Placement Groups
    - Placement Groups are different ways of organizing related EC2 instances.
    - There are 3 types of Placement Groups:
        - Clustered Placement Groups:
            - A cluster placement group is a grouping of instances within a single AZ. Placement groups are recommended for applications that need low network latency, high network throughput, or both.
            - Cannot span multiple AZs
        - Spread Placement Group:
            - A spread placement group is a group of instances that are each placed on distinct underlying hardware.
            - Spread Placement Groups are recommended for applications that have a small number of critical instances that should be kept separate from each other.
            - Individual critical EC2 instances
        - Partitioned Placement Groups:
            - When using partitioned placement groups, Amazon EC2 divides each group into logical segments called partitions. Amazon EC2 ensures that each partition within a placement group has its own network and power source. Not two partitions within a placement group share the same racks, allowing you to isolate the impact of hardware failure within your application.
                - You can have multiple instances in a partition group.
            - Use cases: multiple ec2 instances. HDFS, HBase, Cassandra
    - Names for placement groups must be unique within your AWS account.
    - Only certain types of instances can be launched in a placement group (compute optimized, GPU, memory optimized, storage optimized).
    - AWS recommends homogenous instances within clustered placement groups
    - You can't merge placement groups.
    - You can move an existing instance into a placement group, but before you do the instance must be in the stopped state. You can move or remove an instance using the AWS CLI or an AWS SDK, you can't do it via the console yet.
- AWS WAF
    - What is WAF?
        - WAF is a Web Application Firewall that lets you monitor the HTTP and HTTPS requests that are forwarded to CloudFront, an Application Load Balancer, or API Gateway.
        - WAS also lets you control access to your content.
        - WAF is a Layer 7 aware firewall — standard firewalls can only see down to layer 4
    - You can configure conditions such as what IP addresses are allowed to make certain requests or what query strings parameters need to be passed for the requests to be allowed.
    - Then the application load balancer or CloudFront or API Gateway will either allow this content to be received or to give a HTTP 403 status code.
    - At its most basic, AWS WAF allows 3 different behaviors:
        - Allow all requests except the ones you specify
        - Block all requests except the ones you specify
        - Count the requests that match the properties you specify
    - Extra protection against web attacks using conditions you specify. You can define conditions by using characteristics of web requests such as:
        - IP addresses that requests originate from
        - Country that requests originate from
        - Values in request headers.
        - Strings that appear in requests, either specific strings or string that match regex patterns
        - Length of requests
        - Presence of SQL code that is likely to be malicious
        - Presence of a script that is likely to be malicious
    - Probably asked about how to block certain things based on above lists
    - Can also use ACLs for some of these