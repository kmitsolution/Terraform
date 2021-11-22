## Design AWS VPC using AWS Management Console
### Step-01: Introduction
1. Create VPC
2. Create Public and Private Subnets
3. Create Internet Gateway and Associate to VPC
4. Create NAT Gateway in Public Subnet
5. Create Public Route Table, Add Public Route via Internet Gateway and Associate Public Subnet
6. Create Private Route Table, Add Private Route via NAT Gateway and Associate Private Subnet

### Step-02: Create VPC
1. Name: my-manual-vpc
2. IPv4 CIDR Block: 10.0.0.0/16
3. Rest all defaults
4. Click on Create VPC

### Step-03: Create Subnets
#### Step-03-01: Public Subnet
1. VPC ID: my-manual-vpc
2. Subnet Name:: my-public-subnet-1
3. Availability zone: us-east-1a
4. IPv4 CIDR Block: 10.0.1.0/24

#### Step-03-02: Private Subnet
1. Subnet Name:: my-private-subnet-1
2. Availability zone: ap-south-1a
3. IPv4 CIDR Block: 10.0.101.0/24
4. Click on Create Subnet

### Step-04: Create Internet Gateway and Associate it to VPC
1. Name Tag: my-igw
2. Click on Create Internet Gateway
3. Click on Actions -> Attach to VPC -> my-manual-vpc

### Step-05: Create NAT Gateway
1. Name: my-nat-gateway
2. Subnet: my-public-subnet-1
3. Allocate Elastic Ip: click on that
4. Click on Create NAT Gateway

### Step-06: Create Public Route Table and Create Routes and Associate Subnets
#### Step-06-01: Create Public Route Table
1. Name tag: my-public-route-table
2. vpc: my-manual-vpc
3. Click on Create

#### Step-06-02: Create Public Route in newly created Route Table
1. Click on Add Route
2. Destination: 0.0.0.0/0
3. Target: my-igw
4. Click on Save Route

#### Step-06-03: Associate Public Subnet 1 in Route Table
1. Click on Edit Subnet Associations
2. Select my-public-subnet-1
3. Click on Save

#### Step-07: Create Private Route Table and Create Routes and Associate Subnets
#### Step-07-01: Create Private Route Table
1. Name tag: my-private-route-table
2. vpc: my-manual-vpc
3. Click on Create
#### Step-07-02: Create Private Route in newly created Route Table
1. Click on Add Route
2. Destination: 0.0.0.0/0
3. Target: my-nat-gateway
4. Click on Save Route
#### Step-07-03: Associate Private Subnet 1 in Route Table
1. Click on Edit Subnet Associations
2. Select my-private-subnet-1
3. Click on Save

### Step-08: Clean-Up
1. Delete my-nat-gateway
2. Wait till NAT Gateway is deleted
3. Delete my-manual-vpc
