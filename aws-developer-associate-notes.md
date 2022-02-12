#This contains all the notes for the aws-cloud-developer-associate



## Lesson : Understanding EBS volumes 

1. Elastic block store
  2. Storage volumes that you can attach to your ec2 instances 
  3. Think like it is a disc. Each ec2 instance by default comes with a default ebs volume and it is this volume where your os would be installed. And 
      you can add extra. Use them as you would any system disk. works like a file system 
##Feautures: 

designed for mission critical production workloads 
    highly available -- when you provision a ebs volume it will be automatically replicated within a single AZ which protects against hardware failures 
    scalable - dynamically increase the capacity and also change the type of ebs volume without any downtime 

##  Different options : 

###1. General purpuse ssd -gp2 
        
        Balance of price vs performance . 3iops per gib upto max of 16K iops per volume 
        gp2 voumles less than  1tiB can burst upto 3k iops . Very useful for boot volumes and applciation which are not latency sensitive 
    2. gp3 - 
        
        baseline : 3k iops for any volume (1-16GB) 
        upto 16K iops and 20% cheaper than gp2 
        and great for boot volumes and apps which are not latency sensitive 
    3. provisioned iops ssd (io1): 
        
        suited for OLTP
        high perfomance , 
          64kIops per volume, 50K iops per gig 
          use if you need more than 16Kiops
        designed for io intensive applications, large database and latency sensitive workloads 
    4. io2 --latest 
        
        better than io1 and costs the same wow. 
        500iops per gig and upto 99.999% durability . others are designed for less than this 
         io1 usage + higher durability gives you 5 nines of durability 
    5. io2 block express: 
        
        SAN (storage area network in the cloud) 
        highest perfomance and submilli-sec latency . 
        uses EBS block architecture . provides 4 times the throughput and capacity of normal io- above 
        useful for oracle , ms-sql server, usefule for mission critical applications 

    Througput optimized HDD: 
      1. st1 
        
        low-cost hdd . for storing huge amounts of data but we want to access the data frequently . measured in mb/s/terabyte . 
        40MB/s per TB with max of 500MB/s per volume 
        frequenly accesses ,big-data , etl and log-processing . cost effective way to store mountains of data . Cannot be a boot volume. 
    2. Lowest cost option . (sc1 ) 
      
      baseline : 12mb/s t0 max of 80mb per tb
      throughput : 250mbs/per volume
      A good choice for colder data requiring fewer scans per day 
      cannot be a boot volume 
    
    Magnetic standard is the oldeest (legacy ) 

  IOPS vs throghput 
  1. Measures the number of read /write ops per secs . eg-low latency appliations, db-applications 
    vs measure the number of bytes read or written per second . important metric for large datasets , large I/O sizes , complex queries 
    . the ability to deal with large datasets. 

Practical: 
  1. You can actually create a volume based on a snapshot(point of time copy for an older volume ) . Volumes created from encrypted snapshots would be encrypted and vice-versa for non-encrypted ones 




