# Assignment 1 — Create an Azure Virtual Machine using Terraform

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision a complete Azure Virtual Machine environment: a resource group, virtual network, subnet, public IP, network interface, and an Ubuntu 18.04 Linux VM. You will initialize, plan, and apply the configuration, verify the running VM via Azure CLI, and destroy the resources after testing.

---

# Task 1 — Create a New Terraform Project and Define the Infrastructure

## Goal

Create a `terraform-azure-vm` project and define the resource group, virtual network, subnet, public IP, network interface, and Ubuntu 18.04 VM (with username/password authentication and a public IP output) in `main.tf`.

### Evidence

#### Screenshot 1 — VS Code showing `main.tf` and the required Azure resources

![alt text](screenshots/6a.png)
![alt text](screenshots/6b.png)

---

#### Screenshot 2 — `main.tf` showing the public IP output and VM authentication configuration, with the password hidden or redacted

![alt text](screenshots/7.png)

---

# Task 2 — Initialize Terraform

## Goal

Run `terraform init` and confirm the working directory initializes successfully.

### Evidence

#### Screenshot 3 — Terminal showing successful `terraform init` output

![alt text](screenshots/1.png)

---

# Task 3 — Plan and Apply the Configuration

## Goal

Review `terraform plan`, run `terraform apply`, and record the VM's public IP from the Terraform output.

### Evidence

#### Screenshot 4 — Terraform plan summary showing the proposed resources

![alt text](screenshots/2a.png)
![alt text](screenshots/2b.png)
![alt text](screenshots/2c.png)

---

#### Screenshot 5 — Terraform apply output showing successful completion

![alt text](screenshots/3.png)

---

#### Screenshot 6 — Terraform output showing the public IP of the VM

![alt text](screenshots/8.png)

---

# Task 4 — Verify the Deployment

## Goal

Use Azure CLI to confirm the VM was created and is running.

### Evidence

#### Screenshot 7 — Azure CLI output showing the VM name and running status

![alt text](screenshots/9.png)

---

# Task 5 — Destroy the Resources

## Goal

Run `terraform destroy` to clean up the Azure resources after testing.

### Evidence

#### Screenshot 8 — Terminal showing successful `terraform destroy` completion

![alt text](screenshots/10.png)

---

### Notes

Write a short paragraph explaining what you learned or any issues you encountered.

During this task, I learned how to use Terraform to automate the deployment of Azure infrastructure, including a resource group, virtual network, subnet, public IP address, network interface, and Linux virtual machine. I also learned how to configure the AzureRM provider, initialise Terraform, validate the configuration, create an execution plan, and verify resources using Azure CLI. One issue I encountered was initially running Linux commands from Git Bash on Windows, which caused errors with `wget` and `sudo`. I also encountered a permission error when Terraform was run from the Windows-mounted `/mnt/c` directory. I resolved this by working within the WSL/Ubuntu environment and moving the Terraform project to the Linux home directory. Another configuration issue was an invalid VM administrator password and a missing `features {}` block in the AzureRM provider, which I corrected before continuing. Overall, the task improved my understanding of Terraform, Azure infrastructure as code, troubleshooting, and the Terraform deployment lifecycle.

---

# Submission Instructions

- Add all required screenshots in your submission
- Include the VM public IP from the Terraform output
- Do not expose Azure credentials, subscription details, or passwords

---

# Completion Checklist

- [x] Task 1: `terraform-azure-vm` project created with all required resources defined (Screenshots 1–2)
- [x] Task 2: `terraform init` completed successfully (Screenshot 3)
- [x] Task 3: Plan reviewed and `terraform apply` completed, public IP recorded (Screenshots 4–6)
- [x] Task 4: VM verified as running via Azure CLI (Screenshot 7)
- [x] Task 5: `terraform destroy` completed successfully (Screenshot 8)
- [x] Learning/issues paragraph written (Notes)
- [x] No sensitive information exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*
