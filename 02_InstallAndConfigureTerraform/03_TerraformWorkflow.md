
# 🌱 **Terraform Workflow Fundamentals**

Terraform follows a predictable lifecycle for creating and managing infrastructure.
The basic workflow uses four commands:

1. **terraform init**
2. **terraform plan**
3. **terraform apply**
4. **terraform destroy**

Think of them as **setup → preview → deploy → teardown**.

---

# 🧰 **1. `terraform init`**

**Purpose:** Initialize a Terraform working directory.

You must run this **once per project** or whenever you add/change providers or modules.

### What it does:

* Downloads required **providers** (AWS/Azure/GCP/Kubernetes, etc.)
* Downloads **modules**
* Creates the `.terraform/` directory
* Sets up the backend (if using remote state)
* Validates the project structure

### Command:

```bash
terraform init
```

### Output (typical):

```
Initializing provider plugins...
Terraform has been successfully initialized!
```

### When to run `init` again?

* After adding a new provider
* After changing provider version
* After adding/changing modules
* After cloning a repo with Terraform code

---

# 🔍 **2. `terraform plan`**

**Purpose:** Show what changes Terraform *will make* before applying them.

This is a **dry run** (no changes happen yet).

### What it does:

* Compares your code → existing state
* Shows additions, updates, deletions
* Highlights drift (manual changes)
* Helps review changes safely

### Command:

```bash
terraform plan
```

### Output symbols:

* `+` Create resource
* `~` Update in-place
* `-` Delete resource
* `-/+` Replace resource

Example:

```
+ create aws_instance.example
~ update aws_security_group.sg
```

### Why it's important:

* Avoids accidental destruction
* Allows change reviews (GitOps / CI/CD)
* Builds confidence before deployment

---

# 🚀 **3. `terraform apply`**

**Purpose:** Apply the changes required to reach the desired state.

This is where Terraform actually **creates, updates, or deletes** resources.

### Command:

```bash
terraform apply
```

Terraform will show the plan again and ask for confirmation:

```
Do you want to perform these actions?
  Enter a value: yes
```

### Auto-approve (CI/CD):

```bash
terraform apply -auto-approve
```

### After execution:

Terraform updates the **state file** to store the new infrastructure details.

### Key point:

* `apply` is the only command that *changes* infrastructure.

---

# 💣 **4. `terraform destroy`**

**Purpose:** Delete everything created by Terraform.

Used for:

* Testing
* Cleanup
* Automation
* Temporary environments

### Command:

```bash
terraform destroy
```

You will be asked for confirmation:

```
Do you really want to destroy?
```

### Auto-approve:

```bash
terraform destroy -auto-approve
```

### Important:

`destroy` only removes resources Terraform manages (tracked in the state file).

---

# 📘 **Workflow Summary**

Here’s the full Terraform lifecycle in one place:

| Step           | Command             | What it does                  |
| -------------- | ------------------- | ----------------------------- |
| **Initialize** | `terraform init`    | Downloads providers & modules |
| **Preview**    | `terraform plan`    | Shows what will change        |
| **Deploy**     | `terraform apply`   | Creates/updates resources     |
| **Destroy**    | `terraform destroy` | Removes all resources         |

---

# 🔁 **Typical Developer Workflow**

1. Write or modify Terraform code
2. Run `terraform init` (first time only)
3. Run `terraform plan`
4. Review changes
5. Run `terraform apply`
6. Deploy infrastructure
7. Later, run `terraform destroy` if cleanup is needed

---

