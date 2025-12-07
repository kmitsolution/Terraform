
# 🟦 **1. Install Terraform on Windows**

## ✅ **Method 1: Using Windows Package Manager (Recommended)**

If you have **winget** installed:

```powershell
winget install HashiCorp.Terraform
```

That’s it! Terraform is now installed.

---

## ✅ **Method 2: Manual Download**

1. Go to: [https://www.terraform.io/downloads](https://www.terraform.io/downloads)
2. Download **Windows 64-bit ZIP**
3. Extract the ZIP → you get `terraform.exe`
4. Move `terraform.exe` to a folder like:

```
C:\Terraform\
```

5. Add that folder to **System PATH**:

   * Search **Environment Variables**
   * Edit PATH → Add: `C:\Terraform\`

6. Verify installation:

```powershell
terraform -version
```

---

# 🍎 **2. Install Terraform on macOS**

## ✅ **Method 1: Using Homebrew (Recommended)**

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

Upgrade later using:

```bash
brew upgrade hashicorp/tap/terraform
```

---

## ✅ **Method 2: Manual Download**

1. Go to: [https://www.terraform.io/downloads](https://www.terraform.io/downloads)
2. Download **macOS ARM (M1/M2)** or **AMD64 (Intel)** version
3. Extract the ZIP
4. Move `terraform` to `/usr/local/bin/`:

```bash
sudo mv terraform /usr/local/bin/
```

5. Verify:

```bash
terraform -version
```

---

# 🐧 **3. Install Terraform on Linux**

## ✅ **Method 1: Ubuntu/Debian (APT Repository)**

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt install terraform
```

---

## ✅ **Method 2: RHEL/CentOS (YUM Repository)**

```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install terraform
```

---

## ✅ **Method 3: Manual Binary Installation (Works on all Linux)**

```bash
wget https://releases.hashicorp.com/terraform/<version>/terraform_<version>_linux_amd64.zip
unzip terraform_<version>_linux_amd64.zip
sudo mv terraform /usr/local/bin/
```

Verify:

```bash
terraform -version
```

---

# 🎯 **To Confirm Terraform is Installed**

Run:

```bash
terraform --version
```

If Terraform prints a version number → installation successful.

---

Which one do you want next?
