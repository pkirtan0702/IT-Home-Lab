# IT Home Lab – My Personal Learning Journey

Hey! I'm Kirtan, and this repo is basically my personal documentation of everything I've been setting up and breaking (and fixing) in my home lab. I've been using VirtualBox on my main MAC OS PC to spin up virtual machines and practice the kind of stuff that service desk and help desk teams deal with every single day. This isn't a textbook — it's just me writing down what I did, what went wrong, and how I figured it out.
If you're hiring for a help desk or IT support role, this is me showing my work.

## My Lab Setup 

Before I get into the individual labs, here's what my environment looks like:

Host Machine: Windows 10 PC (16GB RAM, i5 processor)
- Virtualization Software: Oracle VirtualBox (free)
- VMs Running:Windows Server 2016 (Domain Controller) — 4GB RAM allocated
- Windows 10 Pro (Client Machine joined to the domain) — 2GB RAM allocated


- Network Mode: Internal Network between the VMs + NAT for internet on the host

I went with VirtualBox because it's free and honestly it gets the job done. Setting up the internal network between the VMs took me a little while to figure out — I kept losing connectivity and had to go back and set the right adapter settings. But once I got it, everything clicked. 
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-15 at 1 43 43 PM" src="https://github.com/user-attachments/assets/e9d907c2-dc4f-47ad-9445-f5a6402a5d86" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-15 at 2 07 24 PM" src="https://github.com/user-attachments/assets/be7375e8-01f6-42ec-92ce-2da914a4868a" />




# 1. Active Directory – Setting Up and Managing Users
This was the first big thing I tackled because honestly, every company I've looked at lists Active Directory as a requirement. I installed the AD DS (Active Directory Domain Services) role on my Windows Server 2016 VM and promoted it to a Domain Controller.
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-15 at 2 10 38 PM" src="https://github.com/user-attachments/assets/9d9d425d-4a53-44cc-a6fb-e89dd57eb048" />

## What I did
After the server was up and running, I went into Server Manager → Add Roles and Features and installed the AD DS role. Once that finished, I ran the post-install wizard to promote the server to a Domain Controller. I created a new forest and named my domain.
From there I started doing the kind of user management tasks I'd expect to handle daily at a help desk.
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-15 at 2 10 39 PM" src="https://github.com/user-attachments/assets/f238e78b-2eaf-4d6f-9729-0a0a9d9ce487" />
## Creating Organizational Units (OUs)
I organized the directory the way a real company might — separate OUs for departments like IT, HR, and Finance. This makes it way easier to apply policies later
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 09 10 PM" src="https://github.com/user-attachments/assets/caf81be9-7fd6-4719-aa5c-beaaf4402ab4" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 09 10 PM (1)" src="https://github.com/user-attachments/assets/ac480a87-3939-4ad4-be3d-9b4bf562be9f" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 09 11 PM" src="https://github.com/user-attachments/assets/75d423d0-94ac-47c8-95a5-ad0b3fb27207" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 09 11 PM (1)" src="https://github.com/user-attachments/assets/b81b7a77-a03c-40fd-a223-d0f9e22e5de7" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 09 11 PM (2)" src="https://github.com/user-attachments/assets/1bdc25df-d748-4555-8d1b-023513a24a37" />






## Creating User Accounts
I created user accounts for each of my fake employees. Here's the kind of thing I was doing in PowerShell to speed it up once I got comfortable
I also did this through the GUI (Active Directory Users and Computers) first so I understood what each field meant before scripting it.
Adding Users to Groups
I created security group like "HelpDesk" and added user accordingly. This came in handy when setting up folder permissions later.
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-15 at 2 10 40 PM" src="https://github.com/user-attachments/assets/a8c7419d-8088-47ed-b6c7-51f29b944e54" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-15 at 2 24 42 PM" src="https://github.com/user-attachments/assets/70802fbc-a5c7-42ad-a3c8-b994c3b19e7c" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-15 at 2 24 43 PM (1)" src="https://github.com/user-attachments/assets/00fb18ae-0330-4751-aca2-93792ac951ea" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-15 at 2 24 43 PM" src="https://github.com/user-attachments/assets/b569bc9a-15c1-4a03-ac96-fdb762570809" />

## What tripped me up
When I first tried to join my Windows 10 VM to the domain, it kept failing. Took me about 30 minutes to realize the client VM's DNS server wasn't pointing to my Domain Controller's IP. Once I fixed that in the network adapter settings, it joined immediately. Lesson learned — DNS is everything in AD.

# 2. Group Policy – Pushing Settings Across the Domain
Group Policy is one of those things that sounds complicated but once you use it a couple of times it makes a lot of sense. The idea is simple — you create a policy and link it to an OU, and every computer or user in that OU gets those settings automatically.
## What I configured
I opened Group Policy Management from Server Manager and started creating GPOs (Group Policy Objects).
## Password Policy
First thing I did was set up a password policy for the whole domain:

Minimum password length: 8 characters
Password must meet complexity requirements: Enabled
Maximum password age: 90 days
Account lockout threshold: 5 invalid attempts
Lockout duration: 30 minutes
This is exactly the kind of policy a help desk team has to work within when users call in saying they're locked out.
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 46 36 PM" src="https://github.com/user-attachments/assets/c869e839-1780-45a9-94d9-636cc9bfcccc" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 46 39 PM" src="https://github.com/user-attachments/assets/a571a076-4c69-4638-a5bd-7fad685c3260" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 46 41 PM" src="https://github.com/user-attachments/assets/aafa7efc-fe0b-48cc-adcf-b4a28880eef0" />
<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 12 46 36 PM (1)" src="https://github.com/user-attachments/assets/8ec7e56f-bb55-4f82-a6bd-9b61bf2aea3b" />
# 3. DNS and DHCP – Making the Network Work
I set up both DNS and DHCP on my Windows Server because in most small to mid-size environments, the Domain Controller handles both. Understanding these two services is huge for help desk because network connectivity issues almost always trace back to one or the other.
##  DHCP Setup
I installed the DHCP Server role through Server Manager, then configured a scope for my internal network:

Scope Name: Helpdesk1
IP Range: 192.168.10.100 – 192.168.10.200
Subnet Mask: 255.255.255.0
Default Gateway: 192.168.10.1
DNS Server: 192.168.10.10 (my Domain Controller)
Lease Duration: 8 days
After activating the scope and authorizing the DHCP server in Active Directory, my Windows 10 client VM picked up an IP address automatically — 192.168.10.100 on the first lease.
## DHCP Reservation
I also set up a DHCP reservation for my client VM so it always gets the same IP. You do this by binding the reservation to the machine's MAC address. Useful for printers or servers that need a consistent IP but you still want managed through DHCP.
## DNS Setup
DNS was already partially configured when I set up Active Directory, but I went in and looked at what was there and added a few things manually for practice.

- Forward Lookup Zone: Helpdesk1 (created automatically with AD)
- Reverse Lookup Zone: I created this manually — it lets you do reverse DNS lookups (IP to hostname)
- A Record: Added a manual A record for fileserver.helpdesk1.local pointing to a static IP.

  To test DNS was working from the client VM:
  cmd
nslookup homelab.local
nslookup fileserver.helpdesk.local
ping SERVER01

<img width="1600" height="1200" alt="WhatsApp Image 2026-05-20 at 1 45 48 PM" src="https://github.com/user-attachments/assets/4609fd67-5211-4821-b031-72f77bdcd5a7" />

# 4.Remote Desktop – Connecting To and Supporting Other Machines
Remote Desktop is something help desk uses constantly. I set it up between my two VMs and practiced different remote support scenarios.
## Enabling RDP on the client
On my Windows 10 client VM, I went to:
System Properties → Remote → Allow remote connections to this computer
I also made sure the Windows Firewall wasn't blocking port 3389 (RDP's default port):
cmd:
netsh advfirewall firewall add rule name="RDP" protocol=TCP dir=in localport=3389 action=allow

## Connecting from the server
From the Domain Controller, I opened Remote Desktop Connection (mstsc) and connected to the client VM by hostname:
Computer: WIN10CLIENT.helpdesk1.local
Username: homelab\Patty
It connected right away. From there I practiced:

- Navigating the user's desktop remotely
- Checking Event Viewer for login errors
- Running ipconfig /all to check network settings
- Checking which programs were installed via Control Panel
- Restarting services from Services.msc

## Other remote tools I looked at
I also explored Windows Quick Assist (built into Windows 10) which is another remote support option — the helper generates a 6-digit code and the person needing help enters it. Great for helping users outside the domain.
And I spent some time with TeamViewer at a basic level since a lot of small businesses use it for external remote support.

# 5.Real Troubleshooting Scenarios I Practiced
This is honestly my favorite section because this is the actual job. I created realistic problems in my lab and worked through them the same way a help desk tech would.

## Scenario 1: "I can't log into my computer"
Symptom: User gets the message "The referenced account is currently locked out."
What I did:

Opened Active Directory Users and Computers on the server
Found the user account
Right-clicked → Properties → Account tab
Checked the "Unlock account" box and clicked OK

Also checked: Event Viewer on the Domain Controller → Windows Logs → Security. Filtered for Event ID 4740 (account lockout). This shows which machine the lockout came from — useful if it keeps happening, which usually means a saved wrong password somewhere.

## Scenario 2: "My mapped drive disappeared"
Symptom: User logs in and their H: drive is missing.
What I checked:

Asked if this happened after a recent password change (saved credentials break when passwords change)
Ran net use from the client to see current drive mappings
Tried mapping manually: net use H: \\SERVER01\UserShare /persistent:yes
If that failed — checked if the share existed and permissions were correct on the server side
Ran gpupdate /force to reapply Group Policy (since the drive was mapped via GPO)

##  Scenario 3: "I can't connect to the internet / no network"
Symptom: User's network icon shows "No Internet" or they can't reach anything.
My troubleshooting flow:
1. ipconfig /all       → Check if IP is 169.x.x.x (APIPA = DHCP failure)
2. ipconfig /release   → Release current IP
3. ipconfig /renew     → Request new IP from DHCP server
4. ping 192.168.10.10  → Ping the Domain Controller/DNS server directly
5. ping google.com     → Test external connectivity
6. nslookup google.com → Test DNS resolution separately
On the server side, I checked the DHCP console to make sure the scope was active and had available leases.

## Scenario 4: "I forgot my password"
Symptom: User can't log in — password expired or forgotten.
What I did:

Verified the user's identity (in a real environment, you'd follow company policy — employee ID, manager confirmation, etc.)
Opened ADUC, right-clicked the user → Reset Password
Set a temporary password
Checked "User must change password at next logon"
Confirmed account wasn't locked (unlocked it if it was)
Walked the user through logging in with the temp password and setting a new one

## Scenario 5: "An application keeps crashing"
Symptom: User says a program crashes immediately when they open it.
Steps I went through:

Tried running the app as Administrator (right-click → Run as Administrator)
Checked Event Viewer → Windows Logs → Application — looked for error entries around the time of the crash
Checked if the app needed a specific Visual C++ Redistributable or .NET version
Tried a repair install through Programs and Features
Checked available disk space
Ran sfc /scannow to check for corrupted system files

## 7. User Account Issues – Password Resets and Lockouts
This is probably the #1 ticket type at most help desks, so I spent extra time here.
Account Lockout Policy (what I configured)
In my domain's Default Domain Policy:

Lockout threshold: 5 failed attempts
Observation window: 30 minutes
Lockout duration: 30 minutes (auto-unlocks, or manual unlock by admin)

Finding where lockouts are coming from
When a user keeps getting locked out repeatedly, it's usually a saved old password somewhere — a phone, mapped drive, or scheduled task. Here's how I tracked it down:

Opened Event Viewer → Security log on the Domain Controller
Filtered for Event ID 4740 (account lockout)
The event shows the Caller Computer Name — the machine causing the lockouts
Remoted into that machine and checked:

Credential Manager (saved Windows credentials)
Mapped drives with saved credentials
Any scheduled tasks running under that user account
Mobile devices connected to email/Exchange with old password

## 8. What I Learned and What's Next
Doing all of this has honestly been the most useful thing I've done for my IT career so far. Reading about Active Directory and actually building it, breaking it, and fixing it — those are very different experiences. A few things that really stood out:

DNS is the foundation of everything in a Windows domain. When something weird is happening, check DNS first. Seriously.
Documentation matters. Writing this up as I went forced me to actually understand what I was doing, not just click through things and forget.
Event Viewer is your best friend for troubleshooting. I used to ignore it. Now it's one of the first places I check.
PowerShell is worth learning early. The GUI is great for understanding concepts, but once things clicked, PowerShell was faster and more powerful.

What I'm working on next

File Server and NTFS Permissions — Setting up shared folders and practicing the principle of least privilege
Windows Imaging — Learning how to image a machine and deploy it (looking at MDT)
Basic Networking deeper dive — Subnetting, VLANs, and setting up pfSense as a firewall VM

# Tools & Technologies Used
- Oracle VirtualBox : Virtualization platform
- Windows Server Domain:  Controller, DNS, DHCP
- Windows 10 Pro : Client machine / end-user simulation
- Active Directory Users & Computers : User and group management
- Group Policy Management:  ConsoleGPO creation and management
- Remote Desktop:  (mstsc)Remote support 
- Event Viewer: Log analysis and troubleshooting
- Command Prompt :Network diagnostics (ipconfig, ping, nslookup, netstat)

How to Reach Me

LinkedIn: https://www.linkedin.com/in/kirtan-patel-a1610b196/
Email: Pkirtan0702@gmail.com

Thanks for checking this out. This is all self-driven — I built this lab because I genuinely want to be good at this job, not just talk about it in interviews.













