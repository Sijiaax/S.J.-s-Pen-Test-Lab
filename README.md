
# 🚩 Sijia's Penetration Testing Journey

Welcome! This repository hosts the lab notes, command cheat sheets, and technical resources for my YouTube videos. I am documenting my journey from learner to pentester.

## 📺 Connect
* **YouTube Channel:** https://www.youtube.com/@sjpentestlab
* **Linkedin:** https://www.linkedin.com/in/sijia-axford-59a58a294/

---

## 📂 Video Resources

# ☁️ AWS S3 Enumeration & Lateral Movement Lab

## 🚨 Scenario
Targeting a development bucket (`dev.huge-logistics.com`), this lab demonstrates how a simple misconfiguration (Anonymous Read Access) can lead to a full account compromise involving PII data theft.

## ⚡ Attack Chain
1.  **S3 Enumeration:** Identified open `shared` folder allowing anonymous downloads.
2.  **Secret Hunting:** Extracted `hl_migration_project.zip` containing a PowerShell script with **hardcoded AWS credentials**.
3.  **Lateral Movement:** Authenticated as user `pam-test` (via `aws configure --profile`).
4.  **Privilege Escalation:** Used `pam-test` access to download `test-export.xml`, revealing **IT Admin credentials**.
5.  **Data Exfiltration:** Authenticated as `it-admin` to bypass 403 errors on the `admin/` folder and download credit card data (PII).

## 🛠️ Tools Used
* **AWS CLI**: For bucket interaction and identity verification (`sts get-caller-identity`).
* **Bash/Unzip**: For file analysis.
* **Manual Code Review**: Identifying hardcoded secrets in `.ps1` and `.xml` files.

## 🛡️ Remediation (Blue Team Notes)
To prevent this, the organization should:
1.  **Disable Public Access:** Enforce "Block Public Access" settings on S3 buckets.
2.  **Secrets Management:** Never hardcode keys in scripts (`migrate_secrets.ps1`). Use **AWS Secrets Manager** or **IAM Roles**.
3.  **Least Privilege:** Ensure `migration-files` are not readable by standard users like `pam-test`.


### 🔹 AWS IAM Enumeration (Manual vs PACU)
* **Video:** https://youtu.be/O7AfurK85rQ
* **Lab Provider:** Cyber
* **Tools Used:** AWS CLI, PACU
* **Resources:**
    *(https://github.com/Sijiaax/S.J.-s-Pen-Test-Lab/blob/main/Labs/Cybr/Cybr_-_Introduction_to_AWS_IAM_Enumeration-93112c93-e8ca-503f-abf9-2b4d9ae9255e.md) - All the CLI commands used in the video.

### 🔹 AWS Elastic Beanstalk
* **Video:** https://youtu.be/AXMTKARiz40; https://youtu.be/gj7vZ4HXHg8; https://youtu.be/T-wismbixI4
* **Summary:** Exploring defensive architecture and vulnerabilities in Elastic Beanstalk.

---

## ⚠️ Disclaimer
These resources are for educational purposes and authorized testing only. Always ensure you have permission before testing any system.
