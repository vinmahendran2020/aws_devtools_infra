# DevxTools Application Deployment

This document will go over each of the items required to deploy the AWS environment, Jira, Confluence, and Mattermost using Cloud Formation templates.

## Prerequisites

Have a working knowledge on the following:
* AWS VPC and all that is required to stand up a VPC
* CloudFOrmation
* CodeCommit
* SystemsManager
* Jira
* Confluence
* Mattermost

## AWS Environment/Jira

This section will go over each of the items required to deploy a new VPC and all required items as well as Jira.

### Installation

#### Deployment Steps
1. Log into AWS Account and navigate to CodeCommit
2. Navigate to Repositories
   * Selectaws-devtools-infra
       * Downloadaws-jira.yaml
3. Navigate to Cloud Formation
       * Click Create Stack
           * Select "With new resources (standard)"
           * Select "Template is ready" and "Upload a template file"
       * Uploadaws-jira.yaml file and click Next
4. Parameters:
   * Jira Setup
       * Jira Product: Select "Software"
       * Version: Type in "8.5.6"
   * Cluster Nodes
       * Enable CloudWatch Integration: Select "Metrics Only"
       * Cluster Node Instance Type: Select "t3.medium"
       * Maximum Number of Cluster Nodes: Type in "3"
       * Minimum Number of Cluster Nodes: Type in "1"
       * Cluster node instance volume size: Type in "50"
       * Deployment Automation Git Repository URL: Type in "https://bitbucket.org/atlassian/dc-deployments-automation.git"
       * Deployment Automation Branch: Type in "master"
       * The Ansible playbook to invoke to initialize the instance: Type in   "aws_jira_dc_node.yml"
       * SSH keyname to use with the repository: Leave blank
       * Custom command-line parameters for Ansible: Leave blank
   * Database
       * The database engine to deploy with: Select "PostgreSQL"
       * The database engine version to use: Select "9"
       * Database instance class: Select "db.r4.large"
       * RDS Provisioned IOPS: Type in "1000"
       * Master (admin) password: Enter password
       * Enable RDS Multi-AZ deployment: Select "true"
       * Application user database password: Enter password
       * Database storage: Type "200"
       * Database encryption: Select "true"
       * Database Storage Type: Select "General Purpose (SSD)"
   * Bastion host provisioning
       * Deploy Bastion Host: Select "false"
       * SSH Key Pair Name: Leave blank
   * Networking
       * Trusted IP Range: Type "0.0.0.0/0"
       * Availability Zones: Select "us-east-2a, us-east-2b"
       * Make instance internet facing: Select "true"
       * AZ1 private IP address block: Type "10.0.0.0/19"
       * AZ2 private IP address block: Type "10.0.32.0/19"
       * AZ1 public IP address block: Type "10.0.128.0/20"
       * AZ2 public IP address block: Type "10.0.144.0/20"
       * SSL Certificate ARN: Type "arn:aws:acm:us-east-2:059821552031:certificate/e517d635-6d3d-4f92-afe5-bfd28afa8df4"
       * IP address block for the VPC: Type "10.0.0.0/16"
   * DNS (Optional)
       * Existing DNS Name: Type "jira-board.devx.systems"
       * Route 53 Hosted Zone: Type "devx.systems"
   * Application Tuning (Optional)
       * Leave default values for all entries within this section
   * AWS Quick Start Configuration
       * Quick Start S3 Bucket Name: Leave default value of "aws-quickstart"
       * Quick Start S3 Key Prefix: Leave default value of "quickstart-atlassian-jira/"
       * ASI Identifier: Leave default value of "ATL-"
5. Click Next
6. Tags
   * Enter required tags (Name, Environment, etc)
7. Permissions
   * IAM role - optional
       * Leave blank
8. Advanced Options
   * Stack Policy
	   * Leave Default Values
   * Rollback configuration
	   * Leave Default Values
   * Notification Options
	   * Leave Default Values
   * Stack Creation Options
	   * Rollback on failure: disabled
	   * Termination Protection: disabled
9.	Click Next
10.	Review each section to confirm parameters have been input correctly
11.	Check Acknowledgement boxes
12.	Click Create Stack
13.	After Stack has been created successfully, review all services to ensure they have been deployed successfully
    * VPC
    * Networking
      * Subnets
      * Security Groups
      * Routing Table
      * Internet Gateway
      * NAT Gateway
    * EC2
    * Load Balancer
      * Target Group
    * RDS
## Confluence

This section will go over each of the items required to deploy Confluence

### Prerequisites

VPC & Jira in above sections will need to be completed before deploying Confluence

### Installation

#### Deployment Steps
1.	Log into AWS Account and navigate to CodeCommit
2.	Navigate to Repositories
	* Selectaws-devtools-infra
3.  Downloadaws-confluence.yaml
4.  Navigate to Cloud Formation
5.  Click Create Stack
    * Select "with new resources (standard)"
6.  Select "Template is ready" and "Upload a template file"
7.  Uploadaws-confluence.yaml file and click Next
8.  Enter Stack Name (ex:aws-confluence)
9.  Parameters:
    * Confluence Setup
      * Collaborative editing mode: None
      * Version: 7.4.3
    * Cluster Nodes
      * Enable CloudWatch integration: Metrics Only
      * Cluster node instance type: t3.medium
      * Maximum number of cluster nodes: 3
      * Minimum number of cluster nodes: 1
      * Cluster node instance volume size: 50
      * Maximum number of Synchrony cluster nodes: 1
      * Minimum number of Synchrony cluster nodes: 1
      * Synchrony cluster node instance type: t3.medium
      * Deployment Automation Git Repository URL: https://bitbucket.org/atlassian/dc-deployments-automation.git
      * Deployment Automation Branch: master
      * Ansible playbook: Leave default - aws-confluence_dc_node.yml
      * Custom command-line parameters for Ansible: Leave blank
      * SSH keyname to use with the repository: Leave blank
    * Database
      * Database Engine: PostgreSQL
      * The database engine version to use: 10
      * Database instance class: db.r4.large
      * RDS Provisioned IOPS: 1000
      * Master (admin) password: Enter password
      * Enable RDS Multi-AZ deployment: true
      * Application user database password: Enter password
      * Database storage: 200
      * Database encryption: true
      * Database storage type: General Purpose (SSD)
    * Bastion host utilization
      * Use Bastion host: false
      * SSH Key pair name: Leave blank
    * Networking
      * Permitted IP Range: 0.0.0.0/0
      * Make instance internet facing: true
      * SSL Certificate ARN: arn:aws:acm:us-east-2:059821552031:certificate/e517d635-6d3d-4f92-afe5-bfd28afa8df4
    * DNS
      * Existing DNS Name: confluence.devx.systems
      * Route 53 Hosted Zone: devx.systems
    * Advanced (Optional)
      * Remember Me cookie expiry:86400
    * Application Tuning
      * Tomcat Context Path: Leave blank
      * Catalina Options: Leave blank
      * Confluence Heap Size Override: Leave blank
      * Synchrony Heap Size Override: Leave blank
      * DB Pool Maximum Size: 60
      * DB Pool Minimum Size: 20
      * DB Timeout: 30
      * DB Idle Test Period: 100
      * DB Max Statements: 0
      * DB Validate: false
      * DB Preferred Test Query: select version();
      * DB Acquire Increment: 1
      * Enable App to Process Email: true
      * Tomcat Accept Count: 10
      * Tomcat Connection Timeout: 20000
      * Tomcat Default Connector Port: 8080
      * Tomcat Enable DNS Lookups: false
      * Tomcat Maximum Threads: 48
      * Tomcat Minimum Spare Threads: 10
      * Tomcat Protocol: HTTP/1.1
      * Tomcat Redirect Port: 8443
    * AWS Quick Start Configuration
      * Quick Start S3 Bucket Name: aws-quickstart
      * Quick Start S3 Key Prefix: quickstart-atlassian-confluence/
      * ASI Identifier:
10.	Click Next
11.	Tags
	* Enter preferred Tags (Name, Environment, etc)
12.	Permissions
	* Leave field Blank
13.	Advanced Options
	* Stack Policy
		* Leave Default Values
	* Rollback configuration
		* Leave Default Values
	* Notification Options
		* Leave Default Values
	* Stack Creation Options
		* Rollback on failure: disabled
		* Termination Protection: disabled
14.	Click Next
15.	Review each section to confirm parameters have been input correctly
16.	Check Acknowledgement boxes
17.	Click Create Stack
18.	After Stack has been created successfully, review all services to ensure they have been deployed successfully
    * EC2
    * Loadbalancer/Target Group
    * RDS
    * Security Group
## Mattermost

This section will go over each of the items required to deploy Mattermost

### Prerequisites

VPC & Jira will need to be deployed before deploying Mattermost

### Installation

#### Deployment Steps
1.	Log into AWS Account and navigate to CodeCommit
2.	Navigate to Repositories
	* Selectaws-devtools-infra
3.	Downloadaws-mattermost.yaml
4.	Navigate to Cloud Formation
5.	Click Create Stack
	* Select “With new resources (standard)"
6.	Select “Template is ready” and “Upload a template file”
7.	Uploadaws-mattermost.yaml file and click Next
8.  Enter Stack Name (ex:aws-mattermost)
9.	Parameters:
	* S3 bucket location for DB Stack:
	    * https://cf-templates-1o7vzyb9kzbol-us-east-2.s3.us-east-2.amazonaws.com/AWSAuroraPostgres.yaml
	* Database Engine: Amazon Aurora PostgreSQL
	* Database Engine Version: 10
	* Database instance class:
		* There are many options here to choose from. For the current deployment db.r4.large was utilized
	* RDS Povisioned IOPs: 1000
	* Master (admin) password:
		* Enter password for the master account for the db.
	* Enable RDS Multi-AZ deployment: true
	* Application user database password:
		* Enter password used by Confluence
	* Database Storage: 200
	* Database Encryption: true
	* Database Storage Type: General Purpose (SSD)
10.	Networking
	* Permitted IP range
		* To enable public access for Mattermost enter: 0.0.0.0/0
	* Make instance internet facing: true
	* SSL Certificate ARN:
		* arn:aws:acm:us-east-2:059821552031:certificate/e517d635-6d3d-4f92-afe5-bfd28afa8df4
11.	DNS
	* Existing DNS Name: mattermost.devx.systems
12.	AWS Quick Start Configuration
	* MM Identifier: ATL-
13.	Other Parameters:
	* CloudWatch Integration: Metrics Only
	* DBAcquireIncrement: 1
	* DBIdleTestPeriod: 100
	* DBMaxStatements: 0
	* DBPoolMaxSize: 60
	* DBPoolMinSize: 20
	* DBPreferredTestQuery: select version();
	* DBTimeout: 30
	* DBValidate: false
14.	Click Next
15.	Tags
	* Enter preferred Tags (Name, Environment, etc)
16.	Permissions
	* Leave field Blank
17.	Advanced Options
	* Stack Policy
		* Leave Default Values
	* Rollback configuration
		* Leave Default Values
	* Notification Options
		* Leave Default Values
	* Stack Creation Options
		* Rollback on failure: disabled
		* Termination Protection: disabled
18.	Click Next
19.	Review each section to confirm parameters have been input correctly
20.	Check Acknowledgement boxes
21.	Click Create Stack
22.	After Stack has been created successfully, review all services to ensure they have been deployed successfully
	* EC2
	* Load Balancer/Target Group
	* RDS
	* Security Group
	* Mattermost S3 Bucket
23.	Route 53
	* Take note of the Load Balancer internal DNS
	* Create an CNAME Record for the Mattermost URL: mattermost.devx.systems and point it to the Load Balancer Internal DNS Entry

### Backup Procedure

1.	Cron job and script have been incorporated into the .yaml file for Mattermost.
2.  The Cron job will run Monday-Friday at 12am, and will do a bulk export of Mattermost into a .json file with the days date.
3.  Using AWS CLI the script will push the newly created file to the Mattermost/Export/ S3 bucket that was created during Mattermost deployment.
4.  The backup file will be accessible from the S3 bucket.