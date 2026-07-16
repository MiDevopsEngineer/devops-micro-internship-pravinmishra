# Assignment 3 — Production Maintenance Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will treat your already deployed React application (on Ubuntu VM with Nginx) as a live production system. You will perform structured operational checks covering network validation, service health, log analysis, resource monitoring, configuration verification, and incident simulation with recovery — mirroring real on-call DevOps responsibilities.

---

# Task 1 — Server Access & Networking Validation

## Goal

Verify that the deployed React application is reachable from the browser and confirm basic network connectivity of the Ubuntu VM.

### Evidence

#### Screenshot 1 — Browser showing the React app with your Full Name visible on the UI

Add your screenshot here.

---

#### Screenshot 2 — Output of `ip a`

Add your screenshot here.

---

#### Screenshot 3 — Output of `sudo ss -tulpen`

Add your screenshot here.

---

#### Screenshot 4 — Output of `sudo ufw status`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What proves Nginx is listening on 0.0.0.0:80?**

Write your answer here.

---

**2. What proves SSH is active on port 22?**

Write your answer here.

---

**3. Did you find any unexpected open ports? Explain briefly.**

No unexpected open ports were found. The only externally accessible TCP ports are 22 (SSH) and 80 (HTTP), which are required for remote administration and serving the React application through Nginx. Other ports, such as 53 (DNS), 68 (DHCP), and 323 (Chrony), are system services that are either bound to the local loopback interface or used internally by the operating system, so they are not exposed to external users.

---

# Task 2 — Service Health & Systemd Validation (Nginx)

## Goal

Verify that Nginx is properly installed, running, enabled at boot, and safely configured.

### Evidence

#### Screenshot 1 — Output of `systemctl status nginx --no-pager`

Add your screenshot here.

---

#### Screenshot 2 — Output of `sudo nginx -t`

Add your screenshot here.

---

#### Screenshot 3 — Output of `sudo ss -lptn '( sport = :80 )'`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What happens if Nginx fails to restart in production?**

If Nginx fails to restart in production, users may be unable to access the website because the web server is not running. This can result in downtime, failed HTTP requests, and a poor user experience. Administrators should check the Nginx configuration (sudo nginx -t), review the logs (sudo journalctl -u nginx or /var/log/nginx/error.log), fix any configuration errors, and then restart the service. Testing the configuration before restarting helps prevent outages.

---

**2. What's your basic rollback plan?**

Basic Rollback Plan:

1. Keep a backup of the previous application build and the Nginx configuration before deploying changes.
2. If the deployment fails or the website becomes unavailable, restore the previous build files.
3. Restore the previous Nginx configuration if it was modified.
4. Test the Nginx configuration using sudo nginx -t.
5. Restart Nginx using sudo systemctl restart nginx.
6. Verify the website is accessible by opening it in a browser or using curl http://<server-ip>.

---

# Task 3 — Logs & Request Trace

## Goal

Verify real traffic flow and analyze logs to understand system behavior and errors.

### Evidence

#### Screenshot 1 — Output of `sudo tail -n 30 /var/log/nginx/access.log`

Add your screenshot here.

---

#### Screenshot 2 — Output of `sudo tail -n 30 /var/log/nginx/error.log`

Add your screenshot here.

---

#### Screenshot 3 — Output of `sudo journalctl -u nginx --no-pager -n 50`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. Were there any errors in the logs?**

- If yes, mention 1–2 example error lines from the logs and explain what each one means in simple terms.
- If no, explain what it means if the error log is empty or shows no recent errors during your check.

No, there were no errors in the logs during my check. The Nginx error log only contained a notice message:
'2026/07/14 07:30:39 [notice] 26426#26426: using inherited sockets from "5;6;"'
This is an informational message indicating that Nginx reused existing network sockets during a restart or reload. It is a normal part of Nginx's graceful restart process and does not indicate a problem.

---

**2. If there were no errors, what does that indicate about the system?**

Since there were no recent error entries, it means Nginx was running normally and did not encounter any issues loading its configuration or handling requests during the time I checked the log. An empty or error-free log is generally a good sign that the web server is operating correctly, although it does not guarantee that every part of the application is free of issues.

---

**3. Based on the access logs, were your curl requests visible in the log entries? What does that prove about traffic flow?**

Yes. My curl requests were visible in the Nginx access logs. This proves that the HTTP requests successfully reached the Nginx web server, were processed by it, and were logged. It confirms that traffic is flowing correctly from the client (curl) to the server (Nginx), and that Nginx is serving the application as expected.

---

# Task 4 — System Resource Health Check (Capacity Red Flags)

## Goal

Assess server capacity and detect potential performance or failure risks.

### Evidence

#### Screenshot 1 — Output of `uptime`

Add your screenshot here.

---

#### Screenshot 2 — Output of `free -h`

Add your screenshot here.

---

#### Screenshot 3 — Output of `df -h`

Add your screenshot here.

---

#### Screenshot 4 — Output of `sudo du -sh /var/* | sort -h`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. Which resource looks most critical right now? (CPU/load, memory, or disk) Explain why.**

The disk is the most critical resource right now, although it is not under immediate pressure. The root filesystem (/dev/root) is 55% utilized (3.7 GB used out of 6.8 GB), which is higher than the current CPU and memory usage. The CPU load average is 0.00, indicating almost no processor activity, and memory usage is low, with only 389 MiB of 1.9 GiB in use and about 1.5 GiB available. Therefore, disk usage is the resource that should be monitored most closely, even though it still has about 3.1 GB of free space and is operating within a safe range.

---

**2. What happens if disk becomes 100% full in a production server?**

If the disk becomes 100% full on a production server, the server may no longer be able to write new files. Applications can fail to save data, log files cannot be updated, databases may stop working correctly, and users may experience errors or service outages. In severe cases, services such as Nginx may fail to start or restart because they cannot create temporary files or write logs. Monitoring disk usage and cleaning up unnecessary files before the disk is full helps prevent downtime.
---

# Task 5 — Configuration & Deployment Verification

## Goal

Ensure the correct React build is deployed and Nginx is serving it properly.

### Evidence

#### Screenshot 1 — Output of `ls -lah /var/www/html | head -n 20`

Add your screenshot here.

---

#### Screenshot 2 — Output of `grep -R "Deployed by" -n /var/www/html 2>/dev/null | head`

Add your screenshot here.

---

#### Screenshot 3 — Output of `grep -n "try_files" /etc/nginx/sites-available/default`

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. How do you confirm that the correct version of the application is deployed?**

To confirm that the correct version of the application was deployed, I first checked the contents of the web root directory using ls -lah /var/www/html. The output confirmed that the React production build files, including index.html and the static folder, were present. I then verified that my custom change ("Deployed by <Millicent Amalachukwu Anadi>") was included in the deployed files by searching for the text in the web root using grep. Next, I confirmed that Nginx was serving the application from the correct web root (/var/www/html) by accessing the application through the server's public IP address. Finally, I opened the application in a web browser and verified that it loaded successfully and displayed the latest deployed version, confirming that Nginx was serving the correct application.

---

# Task 6 — Nginx Configuration Failure Simulation

## Goal

Simulate a real-world Nginx misconfiguration and recover the service safely.

### Evidence

#### Screenshot 1 — Output of `sudo nginx -t` showing the syntax error (broken config)

Add your screenshot here.

---

#### Screenshot 2 — Output of `sudo nginx -t` showing syntax ok (fixed config)

Add your screenshot here.

---

#### Screenshot 3 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What caused the configuration failure?**

The configuration failed because I accidentally removed the semicolon (;) at the end of the try_files directive. In Nginx, every directive must end with a semicolon, so removing it created a syntax error. When I ran sudo nginx -t, Nginx detected the error and reported that the configuration test had failed.

---

**2. How did you fix the issue?**

I reopened the Nginx configuration file, added the missing semicolon back to the try_files line, and saved the file. I then ran sudo nginx -t again to verify that the configuration was correct. After the test was successful, I restarted Nginx using sudo systemctl restart nginx.

---

**3. How can you avoid this kind of issue in real production systems?**

To avoid this type of issue in production, I would always test the Nginx configuration with sudo nginx -t before restarting the service. I would also review configuration changes carefully, use version control to track modifications, and make backups of working configurations so I can quickly restore them if needed.

---

# Task 7 — Web Application Failure Simulation

## Goal

Simulate missing deployment content and recover the application safely.

### Evidence

#### Screenshot 1 — Output of `curl -I http://<public-ip>` showing failure (non-200 response)

Add your screenshot here.

---

#### Screenshot 2 — Output of `curl -I http://<public-ip>` confirming recovery (200 OK)

Add your screenshot here.

---

### Notes

Answer the following in your own words:

**1. What caused the application to break in this scenario?**

The application stopped working because the deployed web content directory (/var/www/html) was moved and replaced with an empty directory. Since Nginx serves files from this directory, it could not find the application's index.html and other static files, so it was unable to serve the website correctly.

---

**2. How did you fix the issue and restore the application?**

I removed the empty '/var/www/html' directory and restored the original website files from the backup directory (/var/www/html_backup). After restoring the files, I restarted the Nginx service so it could serve the application again. I then verified the recovery by sending an HTTP request with curl, which returned a successful response.

---

**3. What steps would you take to prevent this kind of issue in real production systems?**

To prevent this type of issue, I would keep regular backups of the deployed application, use version control and automated deployment tools, and test deployments in a staging environment before deploying to production. I would also monitor the application after each deployment and have a rollback plan so the previous working version can be restored quickly if a deployment fails.

---

# Task 8 — Security & Reliability Review

## Goal

Review and reflect on the security and reliability practices applied during this assignment.

### Security & Reliability Notes

Answer the following in your own words:

**1. Why is SSH key-based authentication more secure than sharing passwords?**

SSH key-based authentication is more secure because it uses a pair of cryptographic keys instead of a password. Private keys are much harder to guess or brute-force than passwords, and they are never transmitted over the network during authentication. This reduces the risk of unauthorized access and password theft.

---

**2. Why should only required ports be open on a production server?**

Only the ports required by the application should be open because every open port increases the server's attack surface. Closing unnecessary ports reduces the chances of unauthorized access and helps protect the server from network-based attacks. For example, only ports 22 (SSH), 80 (HTTP), and 443 (HTTPS) should be open if they are needed.

---

**3. Why is it important for Nginx to be enabled on boot?**

Enabling Nginx on boot ensures that the web server starts automatically whenever the server is restarted. This improves reliability because the website becomes available without requiring manual intervention after a reboot or system update.

---

**4. What are the risks of sharing secrets, keys, or credentials publicly?**

Sharing secrets, SSH keys, API keys, passwords, or other credentials publicly can allow unauthorized users to access servers, cloud resources, or sensitive data. This can lead to data breaches, financial loss, service disruption, or the compromise of an entire system. Sensitive credentials should always be stored securely and never committed to public repositories.

---

**5. Why should cloud resources be stopped or terminated when they are no longer needed?**

Cloud resources should be stopped or terminated when they are no longer needed to avoid unnecessary costs and reduce security risks. Unused resources can still be targeted by attackers if they remain online. Cleaning up unused instances, storage, and other services helps save money, improve security, and keep the cloud environment organized.

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

`__________________________`

---

#### Screenshot — Published LinkedIn post

Add your screenshot here.

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- Do not expose sensitive information (keys, passwords, account IDs)

---

# Completion Checklist

- [ ] Task 1: Screenshots (browser, ip a, ss -tulpen, ufw status) + Notes answered
- [ ] Task 2: Screenshots (nginx status, nginx -t, ss port 80) + Notes answered
- [ ] Task 3: Screenshots (access log, error log, journalctl) + Notes answered
- [ ] Task 4: Screenshots (uptime, free -h, df -h, du -sh) + Notes answered
- [ ] Task 5: Screenshots (ls html, grep deployed by, grep try_files) + Notes answered
- [ ] Task 6: Screenshots (nginx -t fail, nginx -t pass, curl recovery) + Notes answered
- [ ] Task 7: Screenshots (curl failure, curl recovery) + Notes answered
- [ ] Task 8: Security & Reliability Notes answered
- [ ] LinkedIn post published and URL submitted
- [ ] Full Name visible in all required screenshots
- [ ] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://pravinmishra.com/dmi  
- 🎓 DevOps for Beginners (Udemy): https://www.udemy.com/course/devops-for-beginners-docker-k8s-cloud-cicd-4-projects/  
- 🎓 Agentic AI DevOps with Claude Code: https://www.udemy.com/course/ultimate-agentic-ai-devops-with-claude-code/  
- 🎓 DevOps with Claude Code: Terraform, EKS, ArgoCD & Helm: https://www.udemy.com/course/devops-with-claude-code-terraform-eks-argocd-helm/  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*