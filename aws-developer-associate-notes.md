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

### Commands
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
- Glacier Instant Retrieval (within millisseconds) , Glacier Flexible Retrieval (within hrs/mins) 

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

### Glacier Flexible Retrieval
- Long term archiving data that needs to be ocassionally used within few hours or minutes . Minimum storage duration 90 days 

### Important points 
    

-   private by default . Only bucket owner can see . 
-   if you want it to be accessed by public then we would need to use bucket policies . Applies at the bucket level 
-   bucket access contorl list -- Bucket ACL's . happens at the object level . Provides fine-grained control to your objects 
-   Also provides s3 access logs . - >Logs are writeten to another s3 bucket 
-   
__S3 Standard > Intelligent-tiering >> All the infrequent >> Glacier deep archive is the smallest__
__For the infrequent access a fee will apply for the retrietval in-addition to storage__

___

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
___

### Cloudfront 

- amazon's cdn 
- Cloud front edge location : This is the location where the contnet is cached
- Cloud front origin : This is the origin of all the files from which the files would come 
- Cloud front distribution : this is the name given to the origin and the configuration settings for the content we wish to distribute 
- Optimzed to work with other aws services and also works with your own server 
- TTL(time to live ) is 1 day . Object is automatically cleared from the cache . would be charged if you want to clear it before . And also can be used with __S3 transfer acceleration__ . Enables fast and secure transfer of files 
- Edge locations are not __only read but write as well__ 
- __S3TransferAcceleration__:
  - When enabled users instead of uploading/downloading files to/from s3 will be doing it to the edge location and then the edge location uploads it optimally . Much faster

##  Configuring cloud front with origin access identity 
___
- All requests should come to the cf-origin from cloud front . No request should reach directly to the origin 
- Create a s3 bucket with static web hosting enabled . Disable the public access . Create a cloud front. Add the url . Make sure to select the (origin access identity) and click on update bucket policy to
  allow access to cloud front. if you want dns then add a new record/alias to route53 and map it to your cloudront . 

### Understanding Cloudfront allowed methods: 

- You set what http methods that are allowed in cloudfront 

### Serverless : 
- Lambda 
- SQS
- SNS
- API Gateway 
- DynamoDB
- S3

#### Lambda : 
- First 1 million requets per month are free and then 0.20 $ per month per 1 million requests 
- Duration : Charged in milliseconds  
- Price per gb . Charged 0.00001667 per gb second . First 400, 0000 Gb second per free 
- Lambda concurrent execution limit :
  - There is a limit to the number of concurrent executions across all the functions in a given region per account . 1000 concurrent execution is the limit 
  - Contact aws support to increase the limit 
  - you get 429 http error  . Invocations will start getting rejected 
### Lambdas and VPC: 
-  It is possible to enable lambda to access resources which are inside a private  vpc. Lambda uses the vpc information to set up ENI (Elastic network interfaces ) using an IP 
  -from the private subnet CIDR range . The security group then allows your function to access resources within your  vpc . Could be done via the cli and also the console 

### Step Functions : 
- Orchestratoin of your serverless applications 
- Manage the logic of your application including sequencing , error handling and retry logic , so your application executes in order , and as expected 
- Also logs every step . Easy to debug 
#### Different types of workflows : 
- Standard workflows: 
  - Long running , durable , auditable workflows that may run for up to a year . Full execution history is available up to 90 days 
  - At most once model : Tasks are never executed more than once unless you explicitly specify retry actions 
  - Non -idempotent actions: Actions that can cause a change in state 
- Express workflows: 
  - Great for high-volume , event-processing-type workloads 
  - At least once . Ideal if there is a  possiblility that an execution might be run more than once or you require concurrent executions 
  - Idempotent : For example transforming an input data and storing that in dynamo db 
  - Identical request : eg - reading data from an S3 bucket 
  - Two types: 
    - Synchronous workflows 
    - Asynchronous workflows 
### X-Ray 
- will help you to debug your applications 
-  X-ray service map provides end to end view of requests as they travel through the map 
- X-Ray integrates with many aws services like DynamoDB , lambda and Api gateway 
- We can also instrument our own applications to send data to X-ray 
- Applications running on EC2 , Elastic bean stalk environment and on-premises systems and ECS 
- for ECS, run the x-ray daemon in its own docker image , running alongside your applicaiton 
- if we want to record application specific information in the form of key-value pairs , use annotations to add user defined keyvalue pairs to your X-ray data, 
  - This allows us to filter , index and search within x-ray
### Api gateway 
-  we can import our own apis to api gateway using swagger
-  suppoorting soap (simple object access protocol) . We can configure api gateway as a soap web service passthrough 
-  Also we can transform the json to xml if you want to use soap 
#### Api gateway caching and throttling 
- caches your endpoints resources  for a default ttl of 300 seconds and returns the cached response 
- default limit 10K rps and 5k concurrent requests per region  . if we exceed the limit then you get 429 error 


### Dynamo DB 
- Consisitent ,single-digit ms latency at any scale 
- Great fit for mobile , web , gaming 
- Integrates very well with lambda and configured for auto-scale 
- Stored on SSD which help to give fast read and writes and spread across 3 geographically data centers 
- 2 consistency models : Eventually consistent reads and strongly conssitent reads 
  - __Eventaully consistent reads__ :  consistency is reached withing a second . Best for read performane 
  - __Strongly consistent reads__ : Always reflect successful writes. Writes are reflected across all 3 locations at one. Best for strongly consistent performance 
  - Also supports ACID transactions now-a-days 
- Contains __2__ types of __primary keys__
  - __Partition Key__  : a unique attribte for example a customer -id, product_id, email address. This is called partition key because this is used as an input to an internal hash function which determines the partition 
    - or the physical location on which the data is stored  
  - __Composite Key(Partition key +sort key )__ :  The sort key is usually the timestamp which is used to make the partition key if your partiion key is not unique 
  -
#### Dynamo DB controlling access 
- authentication and access control is managed with IAm 
- IAm permissions : Create iam users within your AWS account with specific permissions to access and create DynamoDB tables 
- also can create IAM roles , enabling temporary access to DynamoDB
- __Restricting user access__ : IAM condition to restrict user access to only their own records . 
  - Use the condition parameter dyanmodb:LeadingKeys to allows users to access only the items where the partition key matches their user_id 
- __Secondary index__ : Dynamo db allows you to run a query on non-primary key attributes using global secondary indexes and loacl secondary indexes 
  - local secondary index; uses the same partiion key + a different sort to your table. Must be created when you create your table 
  - global seconday index: allows you to select a completely different partition key and a sort key . Can be created any time time , so more flexible
#### Scan vs Query api calls 
- __Query__ : Find items in a table based on the primary key attribute and a  distinct value to search for . Ex - selecting an item with user_id=12 will give you all the attributes of that item 
  - use an optional sort keya name and value to refine your results 
  - you can use the projection expression parameter to return only specific attributes 
  - Resutls are always sorted by the sort key . Reverse the order by settign ScanIndexForward parameter to False 
  - by default all queries are eventually consistent
- __Scan__ : Examines every single items in a table . Use proejction experssion parameter to refine all the attributes that you are looking for 
- __Query__ is faster than a __Scan__ . 
- __Improving performance__
  -  A scan operation on a large table can use up the provisioned throughput for a large table in just a single operation 
  - Set a smaller page size . Runnign a large number of smaller operations will allow other request to succeed without throttling
  - Design your tables such that you can use the __Query, Get or BatchGetItem__ api's 
  - Use parallel scans'. By logically dividing a table or an index into segments and then scanning each segment in parallel
  - also you can isolate scan operations to specific tables and segregate thme from your mission critical traffic
___

### Using Dynamodb api calls 
-  create-table 
-  put-item 
-  get-item : retursn 
-  update-item allows you to edit the attributes of an existing item 
-  update-table : allows you to modify a table 
-  list-tables : retunrs a list of tables 
-  describe-table 
-  scan - reads every item in a table and then use a filter experssion to remove the unwanted ones 
-  query 
-  delete-item 
-  delete-table 
### Dynamodb privisioned throughput 
- measured in capacity units
- when you create a table you specify your requirements in terms of read capacity and write capacity units 
- 1 write capacity =1 * 1Kb write per second 
- 1 read capacity = 1 strongly consitent read of 4 kb per second or 2* eventually consistent reads of 4Kbper second(default)
### Dynamo Db on demand capacity vs provisioned capacity 
- Charges wil apply for read write and storage data 
- Dynamo db instantly scales up and down based on the activity of your application . Great for unpredictable workloads 
- When we want to pay what you use
- Spiky , short-lived peaks 
- Might be difficult to predict the cost that I might be paying 
- Use provisioned capacity when your worklaod is predictable 

  ___You can use the batch-write-item api___ command to write all the items to a table in dynamo Db from a file 

### Dynamodb acceleartor(DAX)  
- is a fully -managed , clustered in-memory cache for DynamoDb 
- Delivers upto 10X read performance imporovement. Microsecond performance for millions of requests per second 
- It is a write-through caching service . Data is written to the cache  and the backedn table at the same time 
- This allows you to point your dynamoDb apoi calls at the dax cluster 
- if the item you are querying is in the cache table (cache hit) , DAX returns the resutl 
- else dax performs a eventually consistent GETItem operation against DynamoDB and returns the result of the API call 
- May be able to reduce the provisioned read capacity on your table and save money on your AWS bill 
- Not suitable for write intensive applications . 

### DynamoDB TTL: 
- Defines an expiray time for your data . Expired items are marked for delete 
- Items get expired and get deleted within the next 48 hours 
- use the TTL attribute and set it to the attrite that you would be using for defining expiration 

### DynamoDB streams 
- time ordered sequence of item level modifications (e.g insert, update, delete) 
- puts all of them at logs that are encrypted and stored for 24 hours 
- typically stremas can be used for auditing or replaying of transactions . but mostly used for triggering other services based on the chages 
- dedicated endpoint for accessing it 
- min amount of data is the primary key that was modified ,deleted 
- also can capture the state of the item before and after change 
- Also can be used to replicate data across multiple tables 
-
### Provisioned throughput exceeded and exponential backoff
- provisioned throughput exceeded is seen when your request rate is too high for the read write capacity provisioned on your DynamoDb table 
- aws sdk automatically retrries the requests until successful 
- if we are not using aws sdk , then we can use the exponential backoff . Requester uses progressively longer waits before retrying again for flow control 
- if after a min this doesn't work , then the request size may be exceeding the throughput for your read/write capacity


### KMS service 
- makes it easy for you  to create and control the encryption keys used to encrypt the data 
- integrates with aws services 
- simple to encrypt your data using keys that we manage 
  - use when you are dealing with sensitive information 
  -  common integrations include s3 , dyanmodb , rds and lambda, Ebs, Elastic file system , cloud trail , efs , developer tools 
- CMK : Customer Master key is capable for encrypt/decrypt  data upto 4kb. It is used to generate/encrypt/decrypt your data key and then you can use the data key to encyrpt/decrypt datakey 
- ___CMK Demo___  : go to KMS under IAM and create the key .
- KMS is a regional service 
- Can select any of the following: 
  - Get from KMS
  - Get from external store 
  - Cloud HSM(hardware security module)  store : get your own single-tenant hardware to generate keys 
- Cannot export KMS keys
### Understanding KMS api calls 
- encrypt
- decrypt 
- re-encrypt
- enable-key-rotation 
- also can generate a data key  -very useful as your data remains in the ec2 instance and does not need to be sent over the network to be encrypted/decrypted 
### SQS 
- poll based service not like SNS which is a push-based service 
- Messages that are picked up by the poller are marked as invisible , there is a visibility timeout where the messages that are currently processing by the poller are not visible to the other instances of the poller 
- Can contain upto 256 KB of text 
- Types: 
  - Standard queues : Best effort orderign , unlimited number of transaxtions and gurantee thtn a message is delivered at least once , occassional duplicates 
  - FIFO queue :  EXactly once processing : limit of 300 transactions per second , no duplicates introduced. Good for banking transacations 
- ___SQS Settings___: 
  - Visibility timeout :  default timeout is 30 seconds .  max time is 12 hours 
  - ShortPolling
    - Returns a response immmediately even if the message queue being polled is empty . can result in lot of empty responses if nothing in the queue . Will still need to pay for these responses 
  - Long Polling : 
    - Periodicaly pools the queue. Does not returns a message until a message queue or long poll times ou t
    - generally preferable to short polling 
### SQS Delay queues: 
- allow the user to postpone the delivery of the new messages to a queue for a fixed number of seconds 
- Messages sent to the queue remain invisible to consumers for the duration of the delay period 
- Default delay is 0 and max is 90 seconds
- For standard queues this does not affect the messages already in the queue but for the FIFO queuee, this also effects the delay of messages already in the queue 
### Best practice for managing large SQS messages using S3: 
- Use s3 to store the messages 
- Can use the amazon SQS extended client librray for java to manage them 
- sqs client library for java allows you to 
  - Specify that messages are always stored in S3 or only messages > 256 KB
  - Send a message which refernces a message object stored in s3 
  - get a message object fromi s3 
  - delete a message object from s3
  -
  -
### SNS 
- Can use to create push notifications 
- uses a pub-sub model : Applications can publish messages to a topic and subscribers can receive messages to a topic
- Durable ; - are stored redundanlty stored in multiple A-Z. pay-as-you 
- highly available 
### Kinesis : 
- A family of services which enables you to collect, process, and analyze streaming data in real-time . Allows you to build custom applications for your own business needs
-  Kinesis Streams: Video and Data streams 
-  Kinesis Firehose : Do ETL on data-streams into  AWS data stores to enable near-real-time analytics with BI tools .
-  Kinesis DAta analytics : Do ETL on data and store in AWs data stores 
-  Inside kinesis streams, data is stored in shards. Each shard has a fixed uniit of capacity . . 5 reads per second with total read of 2mbps adn 1000 writes per second with total of 1mb per second  
- Firehose does not have shards . there is no retention . Messages coming in will either be pickec up by the consuming lamba or stored in one of the aws data stores that we need to configure 
