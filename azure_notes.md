# Diffrent topics taught 
  **Cloud concepts** 
    Azure architecture 
    Compute 
    Networking
    Storage 
    Database 
    Authentication and authorization 
    Solutions on azure 
    Security 
    Privacy complicance and trust 
    Pricing
    Support 

    **Cloud concepts**
- language && economy of cloud computing
  - hA, scalability , elasiticity and agility
  - agility means to rapidly develop and test
- Capital expenditure is the money spent by a business or an organization on acquiring or maintaining fixed assets
- Operational expenditure is the money spent for running a product , business or system on a day-to-day basis, including annual costs
- Consumption based pricing let's you pay only for what you use 
- **CloudService-Models**
  -  Iaas provides servers, storage and networkign as a service
  -  SaaS is when a service is built on top of PaaS like office365
  -  paas is a superset of IaaS and also include middleware, such as database management tools
  -  Serverless azure functions
- **Cloud Architecture-Models**
  - Private cloud - this is azure on your own hardware in a location of your choice . All the benefits of public cloud , but you can lock it down . A lot of staff required
  - Public cloud 
  - hybrid cloud
-  **Azure-Architecture**
  - Regions and AZ's
  - Resource Groups 
    - Everything in azure is inside a resource group
    - Each resource can only exist in one resource group 
    - can contain resources that can exist in multiple regions 
    - can be used for maintaining access control 
    - can give users access to a resource group and everythin in it
    - reources can interact with other resources in different groups
    - a resource group has a location , or region as it stores meta data about the  resouces in it 
    - azure resource manager (ARM). is the middleman for your interaction with your resources 
      - Group resource handling - deploy , manage and monitor resources as a group 
      - consistency = deploying resources from various tools will always result in the same consistent state
      - dependencies = define dependencies between resources to make sure that they don't get  in a fight
      - access control
      - tagging resources to easily identify them for future scenarios 
      - billing - use tagging to stay on top of biling for groups of resource s


### Compute
- VM
- Scale Sets 
- App services
- Azure Container instances
- Azure kubernetes service
  - Azure Container registry 
  - Azure container cluster 
- Windows virutal desktop  as it sounds liek 
- functions basically the lambda 

#### Scale Sets (Iaas) 
- lets you create a group of identical, load-balanced VM's
- scaling up/down and scaling out 
- scale sets are identical vm's . They can be activated or deactivated as neeeded
- A baseline vm for the scale set ensures application stablility . A base line vm is what you copy to make up the scale set vm's 
- as resource usage increases , more vms are activated to distribute the load 
- you only pay for the vm, storage and networkgin resources you use. NOthing additional for scale sets 

#### App Servvies (Paas part ) 
- web apps 
- web apps for containers 
  - Azure Container Instances ( ACI) 
  - 
- api apps can host your data backend services 

### Networking 
- Virtual network
- load balancer 
- vpn gateway 
- applciation gateway 
- express route
- cdn 
-

#### Virtual network 
- allows many types of azure resources to communicate with each other securely 
- Address space - range of ip address that is available 
- Subnets 
  - Resource grouping  
  - Address allocation 
  - Subnet security 
- a vnet belongs to a single region 
- a vnet belongs to a single subscription 
-  Advantages: 
  - Scaling : Adding more Vnets or more addresses to one is simple 
  - High Availability: Peering VNets , using a load balancer, or usinng a VPN gateway all increase availability 
  - Isolation : Manage and organize resources with subnets and network security groups 
- VNet-Peering : 
  - Low latency and high bandwidth 
  - Link separate networks
  - Data Transfer : Transfer data easily between subscriptions in different regions 

- Load balancer
  - Inbound flows : Traffic from internet or local network 
  - Frontend : The access point for the load balancer . All traffic goes here first
  - Backend pool : the vm instances receiving traffic 
  - Rules and health probes : Checks to ensure backend instance can recieve the data 
  - Usage: 
    - Internet traffic :  balance the load of incoming internet traffic into a system or an applciation 
    - Internal networks : A load balancer works well with internal applications 
    - Port forwarding : Traffic can be forwarded toa  specific machine in the backend pool 
    - Outbound traffic: Allow outbound connectivity for backend pool vm's 
- Vpn Gateway: 
  -  
