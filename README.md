# Terraform
## **1. Introduction to Infrastructure as Code (IaC)**

* What is IaC?
* Benefits of Terraform over CloudFormation/ARM/Pulumi
* What Terraform can and cannot do
* Terraform architecture (providers, resources, state, modules)

---

## **2. Install & Configure Terraform + Basic CLI Workflow**

* Install Terraform (Windows/Mac/Linux)
* Install AWS CLI & Azure CLI
* Terraform workflow fundamentals

  * `terraform init`
  * `terraform plan`
  * `terraform apply`
  * `terraform destroy`
* HCL syntax basics

  * Blocks, arguments, attributes
  * Files: `main.tf`, `providers.tf`

---

## **3. Variables, Outputs, Locals & Data Sources**

* Variables: string, number, list, map, object
* Variable precedence & TFVARS
* Outputs and when to use them
* Locals (reusable expressions)
* Data sources: querying AWS/Azure resources
* Best practices for variable structure

---

## **4. Terraform Control Structures**

* Loops

  * `for` expressions
  * `for_each`
  * `count`
* Conditionals
* When to use `for_each` vs `count`
* Common loop patterns (tags, SG rules, multiple subnets)

---

## **5. Terraform Functions, Dynamic Blocks & Provisioners**

* Built-in functions (string, numeric, collection, encoding)
* Dynamic blocks to generate nested structures
* Provisioners

  * `remote-exec`
  * `local-exec`
  * `file` provisioner
* Static vs dynamic provisioning
* When provisioners should be avoided

---

# ☁️ **AWS SECTION**

---

## **6. AWS Provider Setup & Core Concepts**

* AWS provider configuration
* Authentication methods
* Regions & AZs
* Terraform resource structure
* Provider-level features (default tags, versioning)

---

## **7. Secure AWS Authentication & IAM**

* IAM fundamentals
* Terraform IAM users, roles, policies
* Managing AWS access keys securely
* Sensitive variables
* Using environment variables
* Using community IAM modules

---

## **8. Networking on AWS: VPC, Subnets & Routing**

* VPC fundamentals
* Public vs private subnets
* Internet Gateway & NAT Gateway
* Route tables and associations
* DHCP options set
* Designing a standard VPC layout

---

## **9. Compute on AWS: EC2, AMIs, Security Groups & Key Pairs**

* EC2 basics (AMI selection, instance types)
* Security group design
* Key pairs
* Using loops for multiple SG rules
* Using data sources for AMIs

---

## **10. Working with Terraform Modules**

* What are modules?
* Local modules
* Public Terraform Registry modules
* Input variables and outputs for modules
* Root module vs child modules
* Module versioning best practices

---

## **11. Terraform State Management**

* How state works
* Local vs remote backend
* Remote backend with S3
* State locking with DynamoDB
* Workspaces (dev/stage/prod)
* Common state errors & how to fix them

---

## **12. Provisioning & Bootstrapping AWS Instances**

* cloud-init
* `user_data`
* remote-exec for configuration
* Writing bootstrap scripts
* When to choose AMI baking

---

## **13. Full AWS Architecture Build**

A production-like deployment using modules:

* VPC
* Subnets
* Security groups
* EC2
* IAM role
* User data scripts
* Remote state
* Module-based project structure
* Cleanup strategy

---

# ☁️ **AZURE SECTION**

---

## **14. Azure Provider & Authentication Setup**

* Azure subscription & RBAC
* Service principal authentication
* Configure AzureRM provider
* Creating resource groups
* Provider best practices

---

## **15. Azure Networking: VNet, Subnets, NSGs**

* VNet basics
* Subnets
* Network Security Groups
* NSG rules
* Associating NSGs with subnets/VM NICs
* Azure vs AWS networking differences

---

## **16. Azure Virtual Machine & Storage**

* VM creation workflow
* VM images, sizes
* Storage accounts
* Boot diagnostics
* Using locals and loops for Azure resource creation
* Tags and naming standards

---

## **17. Remote State & Full Azure Project Build**

* Azure backend using:

  * Storage account
  * Container
  * Access keys
* Build a mini Azure environment:

  * Resource group
  * VNet
  * Subnet
  * NSG
  * VM
  * Remote backend
* Deploy and clean-up strategies

---

# ⭐ **18. Additional Recommended Topics**

(To make the course industry-ready)

### **19. Terraform Testing**

* `terraform validate`
* `tflint`
* `checkov` for security scanning

### **20. CI/CD With Terraform**

* GitHub Actions
* Terraform Cloud
* Automated plan & apply workflow

### **21. Advanced Terraform**

* Error handling
* Importing resources
* Managing drift
* Custom providers
* Writing reusable enterprise modules

---

