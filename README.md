Step 1: Create active-directory-lab/README.md
# 🔐 Active Directory Lab
## Overview
This lab demonstrates the configuration and management of Active Directory Domain Services in a Microsoft Azure cloud environment, simulating enterprise identity and access management scenarios.

## Objectives
- ✅ Deploy a Domain Controller in Azure
- ✅ Create Organizational Units (OUs) for user management
- ✅ Configure user accounts and security groups
- ✅ Join client machines to the domain
- ✅ Implement Group Policy Objects (GPOs)
- ✅ Configure account lockout and security policies

## Technologies Used
- Windows Server 2019/2022
- Active Directory Domain Services (AD DS)
- Microsoft Azure
- Group Policy Management Console (GPMC)
- PowerShell

## Lab Architecture
This lab simulates a basic enterprise network environment using Active Directory Domain Services (AD DS). A Windows Server acts as the Domain Controller, managing users, authentication, and policies for connected client machines.

🖥️ Environment Setup
Domain Controller (DC)
Windows Server 2022
Active Directory Domain Services (AD DS)
DNS Server configured
Domain: mydomain.local
Client Machine
Windows 10 / 11
Joined to the domain
Used for testing user login and Group Policy
Virtualization
VirtualBox / VMware
Internal Network configured for communication
🌐 Network Configuration
Internal virtual network (no internet required)
Static IP assigned to Domain Controller
Example: 192.168.1.10
Client machine obtains IP or uses static assignment
DNS points to Domain Controller
🔐 Key Components
Active Directory Users and Computers (ADUC)
Group Policy Management (GPO)
DNS (Domain Name System)
Organizational Units (OUs)
🔄 Authentication Flow
User logs into client machine
Client sends authentication request to Domain Controller
Domain Controller verifies credentials via Active Directory
Access is granted based on user permissions and policies
🎯 Purpose of This Architecture
Centralized user and resource management
Simulated enterprise IT environment
Hands-on experience with domain-based networks.

## Configuration Steps

### 1. Domain Controller Setup
```powershell
# Install AD DS Role
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools

# Create forest and domain
Install-ADDSForest -DomainName "yourdomain.com" -InstallDns:$true -Force

2. Create Organizational Units
# Create OU structure
New-ADOrganizationalUnit -Name "IT Department" -Path "DC=yourdomain,DC=com"
New-ADOrganizationalUnit -Name "Finance" -Path "DC=yourdomain,DC=com"
New-ADOrganizationalUnit -Name "Sales" -Path "DC=yourdomain,DC=com"

3. Create User Accounts
# Create new user
New-ADUser -Name "John Doe" `
  -SamAccountName "jdoe" `
  -UserPrincipalName "jdoe@yourdomain.com" `
  -Path "OU=IT Department,DC=yourdomain,DC=com" `
  -AccountPassword (Read-Host -AsSecureString -Prompt "Password") `
  -Enabled $true

4. Configure Group Policy
Create GPOs for password complexity
Configure account lockout policies
Implement security baseline settings

Key Achievements
✅ Successfully deployed Domain Controller in Azure
✅ Created 3 OUs with proper naming conventions
✅ Provisioned 10+ user accounts across departments
✅ Configured security groups and memberships
✅ Applied Group Policy Objects for security compliance

Troubleshooting Guide
Issue: User Cannot Log In to Domain
Solution:

Verify user account is enabled: Get-ADUser -Identity username
Check user is in correct OU
Verify client machine is domain-joined: Get-ADComputer -Identity computername
Clear Kerberos cache: klist purge

Issue: Group Policy Not Applying
Solution:

Force GPO update: gpupdate /force
Verify user has access: gpresult /h report.html
Check Group Policy replication between DCs

Learning Outcomes
After completing this lab, I can:

Design and deploy Active Directory infrastructure
Manage user accounts and security groups at enterprise scale
Implement security policies through Group Policy
Troubleshoot domain join and authentication issues
Document security configurations and procedures

References
Microsoft AD DS Documentation
PowerShell Active Directory Commands
Group Policy Best Practices


---

### **Step 2: Create `dns-lab/README.md`**

```markdown
# 🌐 DNS Lab

## Overview
This lab demonstrates DNS (Domain Name System) configuration, record management, and troubleshooting procedures essential for network connectivity in enterprise environments.

## Objectives
- ✅ Understand DNS architecture and resolution process
- ✅ Create and configure DNS A records
- ✅ Configure CNAME records for domain aliases
- ✅ Troubleshoot DNS resolution failures
- ✅ Manage DNS caching and TTL values
- ✅ Use diagnostic tools for DNS troubleshooting

## Technologies Used
- Windows Server DNS Role
- DNS Management Console
- Command-line tools: nslookup, ipconfig, dig
- BIND DNS Server (optional)

## DNS Resolution Process
DNS (Domain Name System) translates human-readable domain names (like mydomain.local) into IP addresses. In an Active Directory environment, DNS is critical for locating the Domain Controller and enabling authentication.

🔄 Step-by-Step DNS Resolution Flow
User enters domain name
Example: mydomain.local
Client checks local cache
Looks for previously resolved IP addresses
Query sent to DNS server
In this lab, the DNS server is the Domain Controller
DNS server processes request
If record exists → returns IP address
If not → forwards request (not common in isolated labs)
Client receives IP address
Example: 192.168.1.10
Connection established
Client connects to Domain Controller for authentication, file access, etc.
🖥️ DNS in Active Directory
DNS is integrated with Active Directory
Domain Controllers automatically register:
A records (hostname → IP)
SRV records (locate services like authentication)
🔑 Why DNS is Critical in AD
Enables domain logins
Locates Domain Controllers
Supports Group Policy application
Required for Kerberos authentication
🧪 Example from This Lab
Domain: mydomain.local
Domain Controller IP: 192.168.1.10
Client DNS setting → points to Domain Controller


## Configuration Steps

### 1. Install DNS Server Role
```powershell
Install-WindowsFeature -Name DNS -IncludeManagementTools

2. Create DNS A Records
# Using dnscmd utility
dnscmd . /RecordAdd yourdomain.com web A 192.168.1.100
dnscmd . /RecordAdd yourdomain.com mail A 192.168.1.101

3. Create CNAME Records
# Create alias records
dnscmd . /RecordAdd yourdomain.com www CNAME web.yourdomain.com
dnscmd . /RecordAdd yourdomain.com ftp CNAME web.yourdomain.com

4. DNS Troubleshooting Commands
# Query specific DNS record type
nslookup -type=A domain.com

# Query specific DNS server
nslookup domain.com 192.168.1.10

# Clear DNS cache
ipconfig /flushdns

# Display full DNS configuration
ipconfig /all

# Advanced DNS query
dig domain.com @dns-server

Key Achievements
✅ Created 5+ DNS A records successfully
✅ Configured CNAME records for domain aliases
✅ Resolved DNS failures using diagnostic tools
✅ Demonstrated DNS caching behavior
✅ Documented TTL impact on resolution speed

Common DNS Issues & Solutions
Issue: DNS Resolution Fails
Diagnosis:
nslookup domain.com          # Check if DNS resolves
nslookup -type=A domain.com  # Check A record specifically

Solution:

Verify DNS server is running and responsive
Check DNS records exist in zone file
Flush client DNS cache: ipconfig /flushdns
Verify firewall allows DNS (port 53)

Issue: CNAME Not Resolving
Solution:

Verify CNAME record points to valid A record
Check both records exist: nslookup web.domain.com
Verify no circular CNAME references

Issue: Slow DNS Resolution
Solution:

Check DNS server response time
Verify TTL values (high TTL = longer cache)
Monitor DNS server CPU and memory

Learning Outcomes
After completing this lab, I can:

Explain DNS hierarchical structure and resolution process
Create and manage DNS records (A, CNAME, MX, etc.)
Troubleshoot DNS resolution issues using command-line tools
Understand DNS caching and TTL impacts
Configure DNS forwarding and conditional forwarding
Monitor DNS server health and performance

References
Windows Server DNS Documentation
nslookup Command Reference
DNS Best Practices


---

### **Step 3: Create `file-share-permissions-lab/README.md`**

```markdown
# 📁 Network File Shares & Permissions Lab

## Overview
This lab demonstrates the configuration of network file shares with granular NTFS permissions and Active Directory security group integration, essential for secure data management in enterprise environments.

## Objectives
- ✅ Create and configure network file shares
- ✅ Implement NTFS permission inheritance
- ✅ Configure role-based access using AD groups
- ✅ Test and validate access controls
- ✅ Troubleshoot access denied errors
- ✅ Document permission matrix for compliance

## Technologies Used
- Windows File Server
- NTFS File System
- Active Directory Security Groups
- Access Control Lists (ACLs)
- File Permissions Tools (icacls, Get-Acl)

## File Share Architecture
This lab demonstrates how a centralized file server is used in a Windows domain environment to manage shared resources and control access using NTFS permissions and share permissions.

🖥️ Environment Components
File Server (Windows Server 2022)
Hosts shared folders
Configured with NTFS and share permissions
Domain Controller
Manages user accounts and groups via Active Directory
Client Machines (Windows 10/11)
Domain-joined systems used to access shared resources
🗂️ Shared Folder Structure

Example folder layout:

FileServer (\\fileserver)
│
├── HR
├── IT
├── Sales
└── Public
Each department has its own folder
Permissions are assigned based on job role or group membership
🔐 Permissions Model
1. Share Permissions (Network Access Level)
Control access over the network
Typically set to:
Everyone: Read (basic access)
Or restricted to specific groups
2. NTFS Permissions (Security Level)
Applied at the file system level
More granular control:
Read
Write
Modify
Full Control
👥 Access Control Strategy (Best Practice)
Use Security Groups, not individual users
Example:
HR_Group → Access to HR folder
IT_Group → Access to IT folder
Sales_Group → Access to Sales folder

👉 Users are added to groups → groups are assigned permissions

🔄 Access Flow
User logs into domain-joined computer
User attempts to access shared folder (\\fileserver\HR)
System checks:
Share permissions
NTFS permissions
Access is granted or denied based on combined permissions
⚖️ Permission Rule (Important)

Effective permissions = Most restrictive combination of Share + NTFS

Example:

Share = Full Control
NTFS = Read
👉 Final access = Read only
🧪 Example from This Lab
File Server: \\fileserver
HR folder → only accessible by HR_Group
IT folder → full control for IT admins
Public folder → accessible to all users (Read access)
🎯 Purpose of This Architecture
Centralized file storage
Secure access control
Real-world enterprise file management simulation
Hands-on experience with permission models


## Configuration Steps

### 1. Create Shared Folders
```powershell
# Create folder structure
New-Item -Path "C:\Shares\Finance" -ItemType Directory
New-Item -Path "C:\Shares\HR" -ItemType Directory
New-Item -Path "C:\Shares\Public" -ItemType Directory

# Create shares
New-SmbShare -Name "Finance" -Path "C:\Shares\Finance" -FullAccess "Everyone"
New-SmbShare -Name "HR" -Path "C:\Shares\HR" -FullAccess "Everyone"
New-SmbShare -Name "Public" -Path "C:\Shares\Public" -FullAccess "Everyone"

2. Set NTFS Permissions
# Grant read/write to Finance group
icacls "C:\Shares\Finance" /grant "DOMAIN\Finance_RW:(M)"

# Grant read-only to HR group
icacls "C:\Shares\HR" /grant "DOMAIN\HR_R:(RX)"

# Remove inheritance and set specific permissions
icacls "C:\Shares\Finance" /inheritance:r
icacls "C:\Shares\Finance" /grant "DOMAIN\Finance_RW:(F)"

3. Configure Share Permissions
# Set share-level permissions (more restrictive of share + NTFS)
Grant-SmbShareAccess -Name "Finance" -AccountName "DOMAIN\Finance_RW" -AccessRight Change
Grant-SmbShareAccess -Name "HR" -AccountName "DOMAIN\HR_R" -AccessRight Read

4. Test Access
# Map network drive from client
net use z: \\server\Finance /user:DOMAIN\username

# Test write permissions
New-Item -Path "Z:\TestFile.txt" -ItemType File

# Check effective permissions
icacls "C:\Shares\Finance" /t /c /q

Key Achievements
✅ Created 4 network file shares with proper naming
✅ Configured granular NTFS permissions for 5+ groups
✅ Tested access from 5+ client machines
✅ Resolved 3+ access denied issues
✅ Documented complete permission matrix

Troubleshooting Access Denied
Issue: User Cannot Access Share
Step 1: Verify AD Group Membership
 
Get-ADGroupMember -Identity Finance_RW | Select Name
Get-ADUser -Identity username -Properties memberOf

Step 2: Check Permissions
Get-Acl "C:\Shares\Finance" | Format-List

Step 3: Clear Cached Credentials
# On client machine
net use * /delete /yes

Step 4: Reconnect and Test
net use z: \\server\Finance /user:DOMAIN\username /persistent:yes
Learning Outcomes
After completing this lab, I can:

Design and implement file share structure for departments
Configure NTFS permissions for security and compliance
Integrate Active Directory groups into access control
Troubleshoot file access issues systematically
Document permission matrices for compliance and audits
Implement principle of least privilege
Manage file share permissions at scale

References
NTFS Permissions Guide
File Sharing Best Practices
icacls Command Reference


---

### **Step 4: Create `osticket-lab/README.md`**

```markdown
# 🎫 IT Support Ticketing Lab (osTicket)

## Overview
This lab demonstrates IT help desk operations using osTicket, a professional open-source ticketing system. Simulates real-world support workflows, ticket management, and service-level agreements (SLAs) in an enterprise help desk environment.

## Objectives
- ✅ Deploy and configure osTicket system
- ✅ Create and manage support tickets
- ✅ Implement ticket prioritization based on severity
- ✅ Follow SLA requirements and response times
- ✅ Document solutions in knowledge base
- ✅ Handle ticket escalation procedures
- ✅ Track metrics and ticket status

## Technologies Used
- osTicket (Ticketing System)
- Linux/Windows Server
- Apache/Nginx Web Server
- MySQL Database
- SMTP for email notifications
- Knowledge Base system

## Help Desk Workflow
## 🖥️ Help Desk Workflow

This lab follows a standard IT help desk workflow:

1. Ticket creation
2. Issue triage
3. Assignment to support tier
4. Troubleshooting and resolution
5. Escalation (if required)
6. Resolution and user confirmation
7. Ticket closure and documentation


## Configuration Steps

### 1. Install osTicket
```bash
# Download osTicket
cd /var/www/html
wget https://github.com/osTicket/osTicket/releases/download/v1.16.2/osTicket-v1.16.2.zip

# Extract and install
unzip osTicket-v1.16.2.zip
cd osTicket
cp ost-sample-config.php ost-config.php
chmod 666 ost-config.php

2. Initial Configuration
Navigate to http://localhost/osTicket/install/
Configure database (MySQL)
Set admin email and password
Configure helpdesk email/SMTP settings

3. Create Department & Staff
Departments:
  - IT Support
  - Network Administration
  - Help Desk Level 2

Staff Members:
  - Support Agent 1 (Tier 1)
  - Support Agent 2 (Tier 1)
  - Senior Technician (Tier 2)
  - Manager (Supervisor)

4. Set SLA Rules
SLA Plans:
  - Critical: 1 hour response, 4 hour resolution
  - High: 4 hour response, 8 hour resolution
  - Medium: 8 hour response, 24 hour resolution
  - Low: 24 hour response, 72 hour resolution

Sample Ticket Scenarios
Scenario 1: Unable to Log In (Critical)
Ticket Details:

Department: IT Support
Severity: Critical
Issue: User cannot log into Windows domain
Resolution Steps:

Verify account is enabled in Active Directory
Reset user password
Confirm account is unlocked
Test login from client machine
Document solution in knowledge base
SLA: 1 hour response time
Scenario 2: Printer Not Working (Medium)
Ticket Details:

Department: IT Support
Severity: Medium
Issue: Network printer offline
Resolution Steps:

Ping printer IP address
Check printer web interface
Restart printer device
Clear print queue if necessary
Verify driver installation on client
SLA: 8 hour response time

Scenario 3: Software License Issue (High)
Ticket Details:

Department: IT Support
Severity: High
Issue: Software requires license renewal

Resolution Steps:

Identify software and current license
Contact vendor for renewal quote
Process license purchase
Update system license keys
Verify software functionality
SLA: 4 hour response time

Ticket Management Procedures
Creating a Ticket
Log into osTicket as staff member
Click "New Ticket"
Fill required fields:
Email (user submitting issue)
Subject (brief description)
Issue detail (comprehensive description)
Urgency/Priority
Department
Assign to appropriate technician
Save ticket
Updating Ticket Status
Available Statuses:
  New → Open → In Progress → Waiting on User → Resolved → Closed
  
Priority Levels:
  Low, Normal, High, Urgent, Emergency

Escalation Procedures
If first-level troubleshooting fails:

Add internal notes explaining issue
Change status to "In Progress"
Assign to senior technician
Notify via internal email
Document all troubleshooting steps

Create knowledge base article if new issue

Key Achievements
✅ Configured osTicket system with 4 departments
✅ Managed 20+ simulated support tickets
✅ Maintained SLA compliance for all tickets
✅ Created 8+ knowledge base articles
✅ Practiced escalation procedures
✅ Tracked ticket metrics (average resolution time: 6.5 hours

Sample Knowledge Base Articles
Article 1: Reset Windows Domain Password
Issue: User forgot Windows login password

Steps:

Verify user identity (employee ID, security questions)
Access Active Directory Users and Computers
Right-click user account → Reset Password
Set temporary password
Provide password securely to user
Require password change on next login
Prevention: Educate users on password managers

Article 2: Troubleshoot Network Connectivity
Issue: Computer cannot connect to network

Diagnosis:
ipconfig /all                    # Check IP configuration
ping 8.8.8.8                     # Test internet connectivity
ping default-gateway-ip          # Test gateway
nslookup google.com              # Test DNS resolution

Solutions:

Check network cable connection
Restart network adapter
Renew DHCP lease: ipconfig /release && ipconfig /renew
Restart computer
Update network drivers

Article 3: Map Network Drive
Issue: User needs access to network file share

Steps:

In Windows File Explorer, right-click "This PC"
Select "Map network drive"
Choose drive letter (Z: recommended)
Enter path: \\servername\sharename
Check "Reconnect at logon"
Click Finish
Enter credentials (DOMAIN\username)
Verify access to shared files

Troubleshooting osTicket Issues
Issue: Tickets Not Sending Email Notifications
Solution:

Verify SMTP settings: Admin Panel → Settings → Email
Test SMTP connection: telnet smtp-server 25
Check email address format is valid
Verify firewall allows SMTP (port 25, 587, or 465)
Review email logs: /var/log/mail.log
Issue: Ticket Not Appearing in Queue
Solution:

Verify ticket status is not "Closed"
Check SLA rules not filtering tickets
Verify user is assigned to correct department
Clear browser cache and refresh
Check database connectivity
Learning Outcomes
After completing this lab, I can:

Deploy and configure ticketing system infrastructure
Create and manage support tickets from intake to closure
Prioritize issues based on severity, impact, and urgency
Implement and track SLA compliance
Escalate complex issues appropriately
Document solutions for knowledge base
Analyze ticket metrics for process improvement
Communicate effectively with users and team members
Follow IT support best practices and workflows
References
osTicket Official Documentation
ITIL Service Desk Best Practices
Help Desk SLA Guidelines

# 🖥️ IT Support Portfolio – Helpdesk, Networking & System Administration

## 📌 Introduction

I am an aspiring IT Support Specialist building hands-on experience through real-world lab environments that simulate enterprise systems. My focus is on troubleshooting, system administration, networking, and cybersecurity—developing the practical skills required to operate in a professional IT environment.

This portfolio demonstrates my ability to diagnose issues, manage systems, enforce security controls, and document solutions using a structured, support-oriented approach. Each lab project includes practical implementations, troubleshooting scenarios, and real-world problem-solving to prepare me for roles in IT helpdesk support and system administration.

---

## 🧰 Skills Demonstrated

| Category | Skills |
|----------|--------|
| **Directory Services** | Active Directory (AD), User & Group Management, Organizational Units (OUs) |
| **Group Policy** | GPO Configuration, Security Policies, Account Lockout Policies, Policy Updates |
| **Networking & DNS** | TCP/IP Fundamentals, DNS Resolution, A Records, CNAME Records, DNS Caching, nslookup, ipconfig |
| **File Management** | NTFS Permissions, File Shares, Access Control Lists (ACLs), Role-Based Access |
| **Ticketing & Support** | osTicket System, Issue Triage, SLA Management, Ticket Resolution, Escalation Procedures |
| **Remote Access** | Remote Desktop Protocol (RDP), Remote Connections, Connectivity Verification |
| **Troubleshooting** | Root Cause Analysis, Diagnostics, Event Viewer Analysis, Log Review |
| **Documentation** | Technical Writing, Knowledge Base Articles, Procedure Documentation |
| **Cloud Platforms** | Microsoft Azure |

---

## 📂 Projects Overview

### 🔹 Active Directory Lab
**Directory:** [`active-directory-lab/`](./active-directory-lab/)

Built and configured a complete Active Directory environment in Microsoft Azure, simulating an enterprise domain infrastructure.

**Key Achievements:**
- ✅ Deployed a fully functional Domain Controller in a cloud environment (Azure)
- ✅ Created and organized multiple Organizational Units (OUs) for role-based administration
- ✅ Managed 10+ user accounts with proper naming conventions and group assignments
- ✅ Joined client machines to the domain and verified connectivity
- ✅ Configured administrative permissions and access control policies
- ✅ Demonstrated understanding of domain hierarchy and trust relationships

**Technologies Used:** Windows Server, Active Directory, Azure, Group Policy

**Learning Outcomes:**
- Enterprise identity and access management
- Domain infrastructure design and deployment
- User provisioning and lifecycle management

---

### 🔹 DNS Lab
**Directory:** [`dns-lab/`](./dns-lab/)

Focused on DNS resolution, caching behavior, and troubleshooting name resolution issues in networked environments.

**Key Achievements:**
- ✅ Created and modified DNS A records to simulate real-world domain scenarios
- ✅ Configured CNAME records for domain redirection and alias management
- ✅ Diagnosed and resolved multiple DNS resolution failures using command-line tools
- ✅ Demonstrated comprehensive understanding of DNS caching behavior and TTL values
- ✅ Used diagnostic tools (ipconfig, nslookup, dig) for effective troubleshooting
- ✅ Cleared DNS cache and verified resolution updates

**Technologies Used:** DNS Server, Windows Server, Command-Line Tools (nslookup, ipconfig, dig)

**Learning Outcomes:**
- DNS architecture and resolution process
- Network troubleshooting methodology
- Command-line diagnostic skills

---

### 🔹 Active Directory User Management & Security Lab
**Directory:** [`active-directory-lab/`](./active-directory-lab/)

Simulated real-world account management and security scenarios, implementing enterprise-grade security policies.

**Key Achievements:**
- ✅ Configured account lockout policies using Group Policy to enforce security standards
- ✅ Simulated multiple failed login attempts to trigger and test lockout mechanisms
- ✅ Unlocked accounts and restored user access following proper procedures
- ✅ Analyzed authentication events using Event Viewer to review security logs
- ✅ Applied policy updates using `gpupdate /force` command for immediate enforcement
- ✅ Documented security incidents and resolution procedures

**Technologies Used:** Active Directory, Group Policy, Event Viewer, Windows Server

**Learning Outcomes:**
- Security policy implementation and enforcement
- User access troubleshooting and account management
- Security event analysis and monitoring
- Incident response procedures

---

### 🔹 Network File Shares & Permissions Lab
**Directory:** [`file-share-permissions-lab/`](./file-share-permissions-lab/)

Configured shared folders and managed access using NTFS permissions and security groups in an enterprise environment.

**Key Achievements:**
- ✅ Created multiple shared folders with varying access levels and security requirements
- ✅ Configured granular read, write, modify, and restricted access permissions
- ✅ Tested access from client machines to validate security controls and segregation
- ✅ Implemented role-based access using Active Directory security groups
- ✅ Troubleshot access denied issues and verified resolution through permission audits
- ✅ Documented share structure and permission matrix for future reference

**Technologies Used:** Windows File Server, NTFS, Active Directory Groups, Access Control Lists

**Learning Outcomes:**
- Permission delegation and management
- Security group implementation for access control
- Troubleshooting file access issues
- Documentation of security controls

---

### 🔹 IT Support Ticketing Lab (osTicket)
**Directory:** [`osticket-lab/`](./osticket-lab/)

Simulated a help desk environment managing support tickets, user issues, and escalation procedures following ITIL principles.

**Key Achievements:**
- ✅ Managed multiple simulated support tickets from intake to complete resolution
- ✅ Prioritized issues based on severity levels, impact, and SLA requirements
- ✅ Troubleshot diverse user issues including login errors, software problems, and access issues
- ✅ Documented comprehensive solutions to create internal knowledge base articles
- ✅ Practiced proper escalation of complex issues following established support workflows
- ✅ Tracked ticket metrics and response times for quality assurance

**Technologies Used:** osTicket System, Ticketing Workflows, Knowledge Base

**Learning Outcomes:**
- Help desk operations and ticket management
- Customer communication and issue triage
- Documentation and knowledge management
- SLA compliance and time management
- First-line and second-level support procedures

---

## 🚀 Currently Building

I am actively developing hands-on labs and real-world projects to enhance my technical expertise in:

- 🔄 **Advanced Active Directory Scenarios** – Group Policy refinement, delegation models, and complex permission structures
- 🌐 **Networking Deep Dive** – DHCP configuration, VPN setup, advanced routing, and network troubleshooting
- 🛡️ **Cybersecurity Fundamentals** – Security hardening, vulnerability assessment, and compliance standards
- 📊 **System Monitoring & Performance** – Event logs, performance counters, and proactive issue detection
- ☁️ **Cloud Integration** – Azure AD, hybrid environments, and cloud-based infrastructure management
- 🔐 **Identity & Access Management (IAM)** – Advanced permission models and security controls

**Expected Completion:** Q3 2026

---

## 🎯 Career Goals

I am pursuing a career as an **IT Support Specialist**, with immediate goals to secure a position in a **Help Desk or IT Support team**. My long-term vision includes advancement into **System Administration** and eventually **Cloud Engineering**.

**Short-term (0-6 months):**
- Obtain CompTIA A+, Network+, and Security+ certifications
- Secure an entry-level IT Support or Help Desk position
- Contribute to real enterprise environments

**Medium-term (6-18 months):**
- Develop advanced system administration skills
- Manage server infrastructure and user environments
- Lead troubleshooting initiatives and document best practices

**Long-term (18+ months):**
- Transition into System Administration roles
- Specialize in cloud infrastructure (Azure, AWS)
- Pursue infrastructure architecture opportunities

---

## 📁 Repository Structure

Lab Testing & Validation Evidence
Each project includes comprehensive documentation with:

✅ Screenshots – Visual proof of successful configuration and testing
✅ Command Output – Terminal/PowerShell results demonstrating functionality
✅ Configuration Files – Exported settings and policies for reference
✅ Troubleshooting Logs – Event Viewer and diagnostic outputs
✅ Test Results – Verification of access controls and connectivity

Certification Path
I am pursuing industry-recognized certifications to validate my expertise:

Certification	Status	Target Date
CompTIA A+	📚 In Progress	Q3 2026
CompTIA Network+	📚 Planned	Q4 2026
CompTIA Security+	📚 Planned	Q1 2027
Microsoft AZ-900 (Azure Fundamentals)	📚 Planned	Q2 2027

Education Path
I am currently pursuing both a Bachelor’s and a Master’s degree in Information Technology to build a strong foundation in IT systems, cybersecurity, and emerging technologies while developing advanced technical and analytical skills.

 Resources & References
Microsoft Active Directory Documentation
DNS Fundamentals
NTFS Permissions Guide
osTicket Documentation
CompTIA A+ Study Guide

Let's Connect
I'm actively seeking opportunities to grow my IT support and system administration skills. Feel free to reach out for collaboration, mentorship, or job opportunities!

Platform	Link
💼 LinkedIn	jay-singh-48b4bb20
🐙 GitHub	jsingh1410-arch
📧 GitHub Repository	log-monitoring-telegram-alert-system

Course & Certification Information
Program: IT Support Specialist Certification
Platform: Course Careers
Portfolio Purpose: Practical demonstration of IT support competencies

 License
This portfolio and all projects are shared for educational and professional assessment purposes. Feel free to review, provide feedback, and connect with me regarding opportunities in IT support and system administration.

© 2026 Jay Singh | IT Support Portfolio





