#This contains all the notes for the aws-cloud-developer-associate


# Lesson 2 

**Understanding EBS volumes**  

* Elastic block store
* Storage volumes that you can attach to your ec2 instances 
* Think like it is a disc. Each ec2 instance by default comes with a default ebs volume and it is this volume where your os would be installed. 
* And you can add extra.Use them as you would any system disk. works like a file system 

### Feautures :

* designed for mission critical production workloads  
* highly available -- when you provision a ebs volume it will be automatically replicated within a single AZ which protects against hardware failures 
* scalable - dynamically increase the capacity and also change the type of ebs volume without any downtime 

###  Different options : 

1. **General purpuse ssd -gp2** 
        
        Balance of price vs performance . 3iops per gib upto max of 16K iops per volume 
        gp2 voumles less than  1tiB can burst upto 3k iops . Very useful for boot volumes and applciation which are not latency sensitive 
2. **gp3** - 
        
        baseline : 3k iops for any volume (1-16GB) 
        upto 16K iops and 20% cheaper than gp2 
        and great for boot volumes and apps which are not latency sensitive 
3. **provisioned iops ssd (io1)**: 
        
        suited for OLTP
        high perfomance , 
          64kIops per volume, 50K iops per gig 
          use if you need more than 16Kiops
        designed for io intensive applications, large database and latency sensitive workloads 
4. **io2 --latest** 
        
        better than io1 and costs the same wow. 
        500iops per gig and upto 99.999% durability . others are designed for less than this 
         io1 usage + higher durability gives you 5 nines of durability 
5. **io2 block express:** 
        
        SAN (storage area network in the cloud) 
        highest perfomance and submilli-sec latency . 
        uses EBS block architecture . provides 4 times the throughput and capacity of normal io- above 
        useful for oracle , ms-sql server, usefule for mission critical applications 

### Througput optimized HDD : 
    
1.  **st1** 
        low-cost hdd . for storing huge amounts of data but we want to access the data frequently . measured in mb/s/terabyte . 
        40MB/s per TB with max of 500MB/s per volume 
        frequenly accesses ,big-data , etl and log-processing . cost effective way to store mountains of data . Cannot be a boot volume.

1. **Lowest cost option (sc1 )**  
          baseline : 12mb/s t0 max of 80mb per tb
          throughput : 250mbs/per volume
          A good choice for colder data requiring fewer scans per day 
          cannot be a boot volume 
1. __Magnetic standard is the oldeest (legacy )__ 


### IOPS vs throghput :
    
*  Measures the number of read /write ops per secs . eg-low latency appliations, db-applications 
      vs measure the number of bytes read or written per second . important metric for large datasets , large I/O sizes , complex queries 
     the ability to deal with large datasets. 

### Practical :

1. You can actually create a volume based on a snapshot(point of time copy for an older volume ) . 
    Volumes created from encrypted snapshots would be encrypted and vice-versa for non-encrypted ones 

## Elastic Load balancer 

### Types 

1. __Application load balancer__

* balances http and https
* layer 7 os model 
* Support advanced request routing . that means that you can route traffic based on the http-header 

2. **Network Load balancer** 
            
* tcp and high performance
* use for balancing tcp traffic when high performance is needed 
* layer 4 of osi model 
* capable of handling millions of second while maitaining lowest latency 
* most expensive 

3. **Classic**

* legacy . Does both https and tcp 
* supports x-Forwared-For headers and sticky sessions 
* Does also support layer 4 which 
  * X-forwared for allows you to identify the ip addr of the original client trying to connect . Can be used to block by region . 
  * Or can be used to check if that is the region where we are allowed to operate 
* 504 gateway timeout . Means that the application underneath the loadbalancer is down or failiing .
* 
4. **Gateway** 
allows you to balance workloads for 3rd party virtual appliances. such as those applications purchased on marketplace 


### Route 53
* Allows you to map the domain name that I own to the followiing
  * EC2 Instance
  * Load balancers 
  * S3 buckets 
###    Practical learning 4:
*  Try doing a route 53 with alb and should connect to a ec2 instance that has apache http d running 


### Practical 

* Interacting with s3 with cli 
  aws s3 ls 
  aws s3 mb s3://some-bucket-name  <!-- create bucket with some name that is globally unique --!>
  aws s3 cp hello.txt s3://some-bucket-name 
  aws s3 ls s3://some-bucket-name  <!-- wouldlist all the contents of the bucket and the input.txt file should be there --!>
* Pagination
  control the number of items that is returned in the output of the aws cli. Default 
  item number is 1000. 
  for ex: if the number of objects is more than 1000, then the cli internally makes multiple api calls and shows the output in one go 
      but sometimes can cause errors . could be used like this 

###Commands
* Can be used when you get the time out errors . Try changin the number of items that are getting returned
  * aws [options] <command> <subcommand> --page-size=100 
  * aws [options] <command> <subcommand> --max-items=100  <!--return max of 100 items --!>

## Important things about IAm roles 

* secure way to grant permissions to entity that you can trust 
* Examples of their usage  
  * could be a iam user in another account 
  * could be used with  code running on a ec2 instance that nneds to perform some actions on aws resource 
  * coudl be a aws managed service that might need to use that aws resouce 
  * users from corporate directory that use identity federation like SAML
  * Web Identitiy like openId 
  * ` 
 {
  "Effect" : "Allow" , 
  "Action" : "s3", 
  "Resource : "*"
 }
`

## Relational dbs 

* Aurora . more performant than sql server and posgres . 
    * Redshift is for data-wareshousing and  OLAP workloads .
### RDS demo 

* We can also use security groups instead of having cidr block ranges whicle connecting to a rds instance from a ec2 instance
* considerign both the rds and the ec2 are in the same vpc and rds then add to the inbound rules the security group for the ec2 instance 
    and you should be able to connect to the rds instance using a sql client . 
### RDS multi-az deployments and read replicas

* multi az used for disaster recovery 
* read only copy of your primary db . could be anywhere. Can be used for scaling 

### RDS backups and snapshots 

* Database-snapshots : point in time copy of the storage volume 
* automated backup : enabled by default . Also generates transaction logs which can be used to replay transactions 

#### automated backup 

* Perform a point in time recover within a defined retention period. It performs daily backups and and also has transaction logs. 
  During a recovery , aws selects the recent backup and aws replays the transaction logs 
* They are stored in S3. both manual and automatic
* will be done in the defined window
* Storage I/O may be suspened for a few seconds . Latency might increse for a few seconds 
#### database backups

* manual and initiated by the user . 
* They enable to backup to a specifi state  
* no retention period 
* stays even if the original db is removed 

### Encryption 

* is done with kms and doen with aes-256 . 
* rds is going to encrypt all of the underlying storage including all the backups and read replicas 
* Can only be done during the initialization 
* what happens if you have to encrypt a already created db. 
  * Take a snapshot of the currrent db and then enable encryption in the new db . 
  * Remember **the dns would change** so make sure you change it . 

### Elasti Cache 

* Works like normal cache 
* memcached : very simple to use. no persistence or multi az deployments . 
* redis : has all of the ones that memcache is missing but lot more sophisticated. 
* Cannot be a possible solution/help if you have tons of write loads or you are doing olap queries 
___
### Parameter Store : 

* Store all the parameters and this needs to be passed to the aws resources  as a bootstrap script 
* Provides centralized config for secrets and parameters . 

### EC2 instance types :

* On-Deamand : Pay by the hour or second on the type of instance that you run  
* Reserved : Reserve capacity for upto 3 years . Great to use if you have fixed requirements . 72% discount on the hourly charge. 
* Spot : Purchase unused capacity for discount of 90%. Prices change with demand and supply . Can be useful when the app has flexible start and the end times. 
* And if you exceed the tier it gets automatically stopped  
* Reserved Instances : a physical ec2 server . This is used when you have server-bound licenses to reuse or compliance requirements
* Dedicated instances :  has its own dedicated hardware , solely belonging to the customer and they do not share resources with anyone else 

---

### Steps for setting up route 53 :

__S3 Standard > Intelligent-tiering >> All the infrequent >> Glacier deep archive is the smallest pricing__
__For the infrequent access a fee will apply for the retrietval in-addition to storage__ 


- launch an ec2 instance that runs httpd 
- **Hosted-zone** : This is a way to map your domain names to the ec2 instance, alb's or s3 buckets : 
- Go to the hosted zone and create the new record. in the record set up the route between the record name and the application load balancer or the application directly 
- Also make sure if you are using the applicaiton load balancer . It is acceptiing requests in port 80 so do not forget to add a new group 
___
### Chapter 4 

### S3 

  - number of objects that you can store are unlimited . Objects can be of max of 5 TB
  - all AWS accounts share the s3 namespace . Each bucket must be globally unique 
  - Each object corresponds to a key-value store. Key is the name and value is the object . Also sometimes there could be a version id 
  - It is highly available and highly durable . Built for 99.95%-99.99%  availability and there is 11 9's of durability 
  - Lifecycle management and versioning is allowed on a file
  - Secure your data with server-side Encryption. 
      This means you can set up default encryption on  a bucket and that would encrypt all the new objects athat are stored in the bucket 
  - Also has ACL which allows which aws accounts or groups are granted access and the type of access . Can be attached to individual objects 
  - Offers tiered storage 
  - Also has bucket policies that can be added to users. 
  - Not suitable for running a operating system or db 

### S3 Storage classes 

- S3 Standard
- S3 Standard Infrequent access 
- S3 One-zone infrequent acces 
- S3 Glacier and S3 Galcier deep archive 
- Intelligent tiering 
- Relative costs 

### S3 standard 

- high availability and durability . Data is stored redundantly across multiple A-Z's (>=3A'Z) 99.99% availability and 11'9s of durability 
  - Designed for frequent access 
  - Website , Content-distribution and content analytics 
  - Default 

### S3 Standard - Infrequent Access 

- Rapid access
- Pay to access the data 
- minimum storage duration of 30 days 
- same availability and durability
- stored in single AZ . Costs you 20% less than s3 standard 

### Glacier options
- Very cheap . 
- Good for historical data 
- 90 days minimum storage for archive  and 120 days min for deep archive 
- same availability and durability

### Intelligent tiering 
- Gets you 2 tiers. S3 will move your object to the respective tier depending on the frequency with which you access your objects
 
__S3 Standard > Intelligent-tiering >> All the infrequent >> Glacier deep archive is the smallest__

 __For the infrequent access a fee will apply for the retrietval in-addition to storage__


### Important points 
    

-   private by default . Only bucket owner can see . 
-   if you want it to be accessed by public then we would need to use bucket policies . Applies at the bucket level 
-   bucket access contorl list -- Bucket ACL's . happens at the object level . Provides fine-grained control to your objects 
-   Also provides s3 access logs . - >Logs are writeten to another s3 bucket 
-   
__S3 Standard > Intelligent-tiering >> All the infrequent >> Glacier deep archive is the smallest__
__For the infrequent access a fee will apply for the retrietval in-addition to storage__

 
### S3 Encryption 
- Encryption in transit . this is done automatically by ssl/tls 
- Encryption at rest. 
- SSE-S3 encrypt with s3 managed keys. The key itself is encrypted 
- SSE-KMS gives additionals key known as 
- SSE-C Customer provideed keys 
- Client side encryption 
- x-amz-server-side-encryption  : AES257 (SSE-s3 managed keys)
- x-amz-server-side-encryption : ams:kms ( uses sse-kms) 
- Can also be done with bucket policy  by adding the followign condition in the  bucket policy  deny aws:SecureTransport : false

