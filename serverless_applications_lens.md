# AWS Serverless Application Lens

## Definitions
#### Compute Layer
* Lambda
* API Gateway - REST API to execute business logic, manage trafic, authorization, monitoring and API versioning
* Step Functions - orchestrate state and function chaining
	* Long running steps into small Lambda executions or EC2 workers 
#### Data Layer
* DynamoDB + DAX
* S3 + CloudFront
* Elasticsearch - search and full text analytics
* AppSync 
#### Messaging and Streaming Layer
 * SNS - pub/sub patterns using async events 
 * Kinesis
 * Kinesis Data Firehose - stream data into Kinesis Analytics, S3, Redshift, ES
#### User management and identity Layer
* Cognito
####  System monitoring and deployment 
* CloudWatch
* X-Ray
* Serverless Application Model (SAM)
#### Edge Layer
* CloudFront
#### Deployment Approaches 
* All at once
	* reconciling transactions during change for backend easier
    * Risk of downtime for rollback
    * Good for dev environment
* Blue/Green
	* Need real*time (1s) update from CW to know if rollback is needed                
* API Gateway Canaries
* Lambda Version Control 

## General Design Principles
 * Speedy, simple, singular functions 
 * For Scalability, think concurrent requests
 * Share nothing * persist state in state machine and persistent storage
 * Assume no hardware affinity
 * Orchestrate multiple functions of your application with state machines
 * Use events to trigger transactions * just in time processing
 * Design for failures and duplicates (idempotency)

## Scenerios
* RESTful Microservices
* Customer - Make API calls
* API Gateway - hosts RESTful interface 
* Lambda - contains business logic
* DynamoDB - pereistently stores data and scales

 Alexa Skills
 * Output the shortest route 
