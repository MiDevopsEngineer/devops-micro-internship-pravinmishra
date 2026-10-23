# Assignment 3 — Deploy a React Application on Azure Virtual Machine Using Terraform

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will use Terraform to provision an Azure resource group, network, and Ubuntu 20.04 VM, then deploy the `my-react-app` React application onto the VM over SSH and serve it through Nginx.

---

# Task 1 — Create a New Terraform Project

## Goal

Create a `terraform-react-azure` project directory for the Azure Terraform configuration.

### Evidence

#### Screenshot 1 — File Explorer, VS Code, or terminal showing the `terraform-react-azure` project directory

![alt text](screenshots/20.png)

---

# Task 2 — Write main.tf to Provision the Azure Infrastructure

## Goal

Define the resource group, virtual network/subnet, Network Security Group (SSH 22, HTTP 80), public IP, network interface, and Ubuntu 20.04 Standard B1s VM in `main.tf`.

### Evidence

#### Screenshot 2 — VS Code showing `main.tf` with the required Azure resources, with any password or sensitive values hidden

![alt text](screenshots/21.png)

---

# Task 3 — Initialize Terraform

## Goal

Run `terraform init` and confirm the working directory initializes successfully.

### Evidence

#### Screenshot 3 — Terminal showing successful `terraform init` output

![alt text](screenshots/19.png)

---

# Task 4 — Plan and Apply the Configuration

## Goal

Review `terraform plan`, run `terraform apply`, and record the VM's public IP.

### Evidence

#### Screenshot 4 — Terraform apply output showing successful completion

![alt text](screenshots/22.png)

---

#### Screenshot 5 — Azure portal showing the Virtual Machine running and its public IP

![alt text](screenshots/23.png)

---

# Task 5 — Connect to the Virtual Machine

## Goal

Establish an SSH session with the Ubuntu VM through its public IP.

### Evidence

#### Screenshot 6 — Terminal showing a successful SSH connection to the Azure VM

![alt text](screenshots/24.png)

---

# Task 6 — Install Node.js, npm, and Git

## Goal

Update Ubuntu and install Node.js, npm, and Git.

### Evidence

#### Screenshot 7 — Terminal showing successful installation and the `node -v` and `npm -v` output

![alt text](screenshots/25.png)

---

# Task 7 — Clone, Build, and Serve the React App with Nginx

## Goal

Follow the `my-react-app` repository README to clone, install, and build the app, then serve the production build through Nginx.

### Evidence

#### Screenshot 8 — Terminal showing the successful React build

![alt text](screenshots/27.png)

---

#### Screenshot 9 — Terminal showing that Nginx is active and running

![alt text](screenshots/26.png)

---

# Task 8 — Test the Deployment

## Goal

Confirm the React application loads through the VM's public IP and navigation works.

### Evidence

#### Screenshot 10 — Browser showing the React application with the Azure VM public IP visible in the address bar

![alt text](screenshots/28.png)

---

### Notes

Write a short summary of what you built and any issues you encountered and how you resolved them.

I worked on deploying a React application to an Azure Virtual Machine provisioned using Terraform. The assignment involved infrastructure provisioning, configuring the VM, and preparing the environment to serve the React application.

During the process, I encountered SSH connection timeouts when attempting to connect to the VM on port 22. I also worked through Azure CLI authentication and network configuration troubleshooting. I successfully authenticated to my Azure subscription and began investigating the VM's public IP address, network security group rules, and SSH connectivity.

This assignment helped me gain practical experience with Infrastructure as Code, Azure resource management, and troubleshooting cloud networking issues.

---

# Submission Instructions

- Add all required screenshots in your submission
- Include the Azure VM public IP
- Do not expose Azure credentials, passwords, or private keys

---

# Completion Checklist

- [x] Task 1: `terraform-react-azure` project created (Screenshot 1)
- [x] Task 2: `main.tf` defines all required Azure resources (Screenshot 2)
- [x] Task 3: `terraform init` completed successfully (Screenshot 3)
- [x] Task 4: Plan applied and VM running with public IP (Screenshots 4–5)
- [x] Task 5: SSH connection verified (Screenshot 6)
- [x] Task 6: Node.js, npm, and Git installed (Screenshot 7)
- [x] Task 7: React app built and served through Nginx (Screenshots 8–9)
- [x] Task 8: App verified through the VM public IP (Screenshot 10)
- [x] Summary paragraph written (Notes)
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
