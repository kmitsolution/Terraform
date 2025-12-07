
# 🟦 **1. Install AWS CLI**

AWS CLI lets Terraform authenticate and interact with AWS.

---

## ✅ **Windows Installation**

### **Method 1: MSI Installer (Recommended)**

1. Download installer:
   [https://awscli.amazonaws.com/AWSCLIV2.msi](https://awscli.amazonaws.com/AWSCLIV2.msi)
2. Run installer → click Next → Finish
3. Verify:

```powershell
aws --version
```

---

## ✅ **macOS Installation**

### **Method 1: Using Homebrew**

```bash
brew install awscli
```

### **Verification**

```bash
aws --version
```

---

## ✅ **Linux Installation**

### **Method 1: Linux x86_64 Installer**

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

**Verify:**

```bash
aws --version
```

---

## 🧑‍🔧 **Configure AWS CLI**

After installation:

```bash
aws configure
```

Enter:

* AWS Access Key ID
* AWS Secret Access Key
* Default region (e.g., `us-east-1`)
* Output format (`json`)

---

# ☁️ **2. Install Azure CLI**

Azure CLI lets Terraform authenticate with Azure subscriptions.

---

## 🟦 **Windows Installation**

### **Method 1: MSI Installer (Recommended)**

Download:

[https://aka.ms/installazurecliwindows](https://aka.ms/installazurecliwindows)

Run the installer → Finish.

Verify:

```powershell
az version
```

---

## 🍎 **macOS Installation**

### **Method 1: Homebrew**

```bash
brew update && brew install azure-cli
```

Verify:

```bash
az version
```

---

## 🐧 **Linux Installation**

### **Ubuntu/Debian**

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

### **RHEL/CentOS/Fedora**

```bash
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo dnf install https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm
sudo dnf install azure-cli
```

### **Generic Script (all Linux)**

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Verify:

```bash
az version
```

---

## 🔐 **Login to Azure CLI**

```bash
az login
```

A browser will open → choose your Azure account.

Confirm subscription:

```bash
az account show
```

---

# 🎯 **Summary**

| Tool          | Purpose                    | Install Command |
| ------------- | -------------------------- | --------------- |
| **AWS CLI**   | Access AWS from terminal   | `aws --version` |
| **Azure CLI** | Access Azure from terminal | `az version`    |

Both tools are required before running **Terraform with AWS or Azure providers**.

---


