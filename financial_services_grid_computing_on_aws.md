# Financial Services Grid Computing on Amazon Web Services

### Introduction
##### Defining Grid Computing for Financial Services
* large amounts of computional resources, high throughput and predicatable latency networking
* *Compute grid for FS* - massively parallel architectures, optimized throughput, and latency tolerent

##### Compute Grid Architecture & Performance
* reliable results need fixed ratio of compute cores to memory
* increased throughput measured in jobs tasks processed per time period
* grid director/controller forms the compute grid
* clients submit tasks to grid via job manager/broker
* take minutes/hours rather than milliseconds
* calculations partitioned b/w engines and are parallelized (share-nothing architecture)

##### Benefits of Grid Computing in the Cloud
* allocate compute capacity on demand
* VPC gives complete control over network topology, isolation, and security for compliance
* elastic infrastructure based on need at the momment in time
* operations is simplified because of infrastructure as code
* billing simiplified because of tagging

### Grid Computing on AWS
##### Initial Implementation
* EC2 with predefined AMI connected to data center data sources via VPN/Direct Connect
##### Full implmentation
* difference with initial
	* application data source is hosted on AWS
	* placement group
	* grid controller on AWS
###### Security
* AWS compliant with various certifications and third-party attestations
* AWS Shared responsibility model
	* client responsible for OS, platform, apps, security policies, standards and procedures
* IAM, STS
* AD Connector - connect corporate active directory to AWS
* MFA - web console, CLI, s3 delete
###### Patching
* start with base OS image 
* customize with services, patches, configurations, and IDS
* use AMI to launch HPC grids
* CloudFormation to stand up single instances from current AMI, patch + update, create new AMI
* older instances decommisioned using tagging policies 
###### Networking
* static or dynamic routing protocols to provide a single routing domain or dynamic reachability info b/w internal and cloud
* placement groups - bisected 10 GB networking b/w EC2 
###### Distribution of Static Data Sources
* ex. grid software, holiday calendars, gridlibs
* option 1: bake it into AMI - must be rebuild after changes
* option 2: store on on-premieses source, S3, or CodeDeploy
###### Access to Dynamic Data Sources
* RDS, DynamoDB, Intel Lustre
* AWS Direct Connect = preditable throughput and latency
###### Cluster Machine Availability & Workflow
* start-up thousands of instances using AWS cfn-cluster or MIT StarCluster or Bright Cluster Manager or Cycle Computing CycleCloud
* use spot instances to minimize costs
###### Result Aggregation & Client Scaling
* data grids that aggregate results from compute grids (mapreduce)
* S3, Glacier
###### High Availability
* best practice to run across multi-AZ
* Grid computing best within single AZ
* inter data transfer is minimized 
* capabable of rebuilding the grid in alternate AZ
###### Repeatable Assembly
* CloudFormation
* repeatable assembly of resources
* use in performance and functional testing
###### Compute Regions & Locations
* satisfy regulatory and compliance requirements 
###### Instance Type Considerations
* Recommend C4 instance type 
###### Spot Instances
* spot fleet allow you to descibe the total capacity you require in terms of CPU and RAM for varity of different instance types

