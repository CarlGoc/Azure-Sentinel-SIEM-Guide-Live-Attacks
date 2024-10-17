![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/638bb5474a8edcd0ee2497339c7f7ab9662bf63a/Images/Azurelogo.jpeg)

<h1>Beginner's Guide to SIEM: Azure Sentinel Tutorial with Live Cyber Attack Mapping!</h1>

<H2>Description</H2>

Learn the basics of Security Information and Event Management (SIEM) with this beginner-friendly Azure Sentinel tutorial. Discover how to set up and use Azure Sentinel to monitor, detect, and respond to live cyber threats in real time. This step-by-step guide covers essential features and demonstrates how to map live cyber attacks, providing you with practical skills to strengthen your organization's security posture.

<H2>Environments Used</H2>

- <b>Azure Portal</b> - a cloud computing service operated by Microsoft for application management via Microsoft-managed data centers
- <b>Services within Azure</b> : Log Analytics Workspace and Sentinel (Microsoft's SIEM)
- <b>Powershell</b>
- <b>Remote Desktop Protocol</b>

<H2>Overview</H2>

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/9fda96bf9f278d62879921d668de4b11854572e9/Images/Diagram.jpg)

<H2>Step 1: Create FREE Azure Account</H2>

[Azure](https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account?icid=azurefreeaccount)

<H2>Step 2: Create our honey pot virtual machine</H2>

- In the search bar of the “Quickstart Center” page > search and click virtual machine
- This will be the honey pot virtual machine made to entice attackers from all over the world

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/9daed51c35407b62ba0145788e0382e3aec49c48/Images/s2.png)

<H2>Step 3: On the "virtual machines" page click Create > Azure Virtual Machine</H2>

- Edit the virtual machine as follows:
- Click create new under resource group and name it Honeypotlab (this resource group is a logical grouping of similar resources)
- Name the virtual machine: honeypot-vm
- Under region select: (Canada) Canada Central
- Under Image select: Windows 10 pro, version 22H2 - Gen2
- Availability zone: Zone 1
- Under size: Standard_B1s - 1 vcpu, 1 GiB memory
- Create a username and password - <b>don’t forget credentials</b>
- Finally, check confirm box - leaving the rest in their default options

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/5fc01e413251b84c14da2362b1fde348054e0945/Images/s3.png)

<H2>Step 4: Click > Next: Disk but leave it as is, click to continue to Networking</H2>

- Under NIC network security group select > Advance and under Configure network security group select Create new
- You should see a default rule (something like 1000: default-allow-rdp), click the three dots to the right of it and remove it.
- Select Add an inbound rule
- Match the settings of the new rule as follows:
- Set Destination port ranges: *
- Priority: 100
- Name: DANGER_ANY_INBOUND
- Leave the rest of the settings as default
- Click Add > OK > Review + create - wait a bit to load and click Create

![image alt](https://github.com/Tony-91/sentinel_attack_heatmap/blob/3da6d24549130a3456c3af6d1d12ff11c0615cb9/images/S4.png)

<H2>Step 5: Create Log Analytics workspace</H2>

- As we wait for our vm to deploy, go back to the search bar and search and click Log Analytics workspaces

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/5fc01e413251b84c14da2362b1fde348054e0945/Images/s5.png)

- Select the blue Create log analytics workspace button
- Under the Basics tab:
- Resource source group: Honeypotlab
- Name: lawhoneypot1
- Region: West US
- Click Review + Create and click Create

<H2>Step 6A: Enable log collection from vm to log workspace</H2>

- Back in the search bar search and click Microsoft Defender for Cloud
- Once on the dashboard click > Environment Settings > (through the drop down menus) > lawhoneypot1

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/5fc01e413251b84c14da2362b1fde348054e0945/Images/s6a.png)

<H2>Step 6B: Under lawhoneypot1 select Defender Plans and enable Servers ON and SQL servers on machines OFF. With Cloud Security Posture Management ON. Hit save.</H2>

- Under Data Collection tab select All Events. Hit save

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/757db9ab8665132ae84046771029e98f098dddda/Images/s6b.png)

<H2>Step 7: connect Log Analytics workspace to our vm</H2>

- On the search bar select Log Analytics workspace
- Select lawhoneypot1 > Virtual Machines > honeypot-vm
- Click connect, after clicking honeypot-vm
- It will take some time to successfully connect; you should get a message confirming connection.

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/5fc01e413251b84c14da2362b1fde348054e0945/Images/s7.png)

<H2>Step 8: Add Microsoft Sentinel to our workspace</H2>

- In search bar find Microsoft Defender for cloud
- Click Create Microsoft Defender for cloud > select lawhoneypot1 > Add
- This will also take some time

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/5fc01e413251b84c14da2362b1fde348054e0945/Images/s8.png)

<H2>Step 9A: Log into vm through host machine</H2>

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/5fc01e413251b84c14da2362b1fde348054e0945/Images/s9a.png)

<H2>Step 9B: RDP from host Windows machine</H2>

- On your Windows machine (Windows vm will also work) search and open Remote Desktop Connection
- Paste your Azure IP into Computer
- Before connecting, click Display and scale down display configuration for easier viewing
- Click connect
- In the Enter your credentials window click more choices > Use a different account
- Enter invalid credentials tin order to generate a log for later viewing.
- Then, enter your credentials we created for our Azure vm in Step 3, click OK.
- Accept the certificate warning
- You should be logged into the vm when you see “Remote Desktop Connection” at the top of the screen.

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/679aab98ece47cf34f04bef02c3567c56503663c/Images/s9b.png)

<H2>Step 10A: Set up vm and explore</H2>

- Click NO to all privacy settings and Accept
- Set up Edge
- Search and click Event Viewer
- Click Windows Logs > Security and find the Audit Failure log (our failed login attempt; if you don’t see it at first filter current log by “Audit Failure” found to the left)

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/679aab98ece47cf34f04bef02c3567c56503663c/Images/s10A.png)

<H2>Step 10B: Turn off firewall to make vm more susceptible to attack</H2>

- Open command prompt on your host machine and try to ping the Azure vm - it shouldn’t work!
- Search and open wf.msc on Azure vm - remember to keep an eye on vm IP at the very top to confirm you’re in the vm and NOT in on your host to avoid confusion.
- Click Windows Defender Firewall Properties near the middle of the page
- Under the Domain Profile > Firewall state: OFF
- Under Private Profile > Firewall state: OFF
- Under Public Profile > Firewall state: OFF
- Try to ping vm again from your host machine - this should now work!

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/679aab98ece47cf34f04bef02c3567c56503663c/Images/s10b.png)

## Step 11A: Retrieve PowerShell Script
[Download the Script](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/2038d37e8a71d5da07988a80a33c923c8c9ff25f/Script/Script)


- Open Powershell ISE
- For convenience you can copy/paste the code into a new ps1 file and save it to the desktop of the honeypot-vm (remember to see vm IP at the top)
- You will also need an API key, get here: [API key](https://ipgeolocation.io/)
- Create an account and log in
- Copy and paste your API key in your Powershell script $API_KEY = “_your API key_”
- Save file.

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/8f2ce944ef6d0767854d1d492cb745b1823f87d9/Images/s11a.png)

<h2>Step 11B: Powershell script (cont.)</h2>

- Test and run the script pass pressing green play button at top of window
- You should receive purple logs indicating latitude / latitude of failed logins (some sample logs and some log when we failed to log in)

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/8f2ce944ef6d0767854d1d492cb745b1823f87d9/Images/s11b.png)

<h2>Step 12A: Create custom geolocation log in Log Analytics Workspace.</h2>

- This log will use IP information to give us specific geolocation to our create map down the line.
- Search and click Log Analytics Workspace > law-honeypot1 > custom logs > + Add custom log
- We need to upload a sample log to “train” log analytics on what to look for.

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/8f2ce944ef6d0767854d1d492cb745b1823f87d9/Images/s12a.png)

<h2>Step 12B: Custom geolocation log (cont.)</h2>

- Our sample logs are in our honeypot-vm.
- In our honeypot-vm, search RUN > search C:\ProgramData\ and open the failed_rdp file.
- Our failed RDP logins are sent to this txt file, open and copy all the sample logs.
- Back on our host machine, open notes and paste our sample logs.
- Save the file in a log or txt format and upload it in the Create a custom log page. Click next and you should see the sample logs.

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/8f2ce944ef6d0767854d1d492cb745b1823f87d9/Images/s12b.png)

<h2>Step 12C: Click next and under Collection Paths > under Type > Windows, under Path write C:\ProgramData\failed_rdp.log</h2>

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/8f2ce944ef6d0767854d1d492cb745b1823f87d9/Images/s12c.png)

<h2>Step 12D: Click next > under Details > Custom log name write FAILED_RDP_WITH_GEO (CL will be added to the end)</h2>

- Click next > Create >Review + Create
- Let’s go back to log analytics and check if Azure is connected and listening to our vm.

![image alt](https://github.com/CarlGoc/Azure-Sentinel-SIEM-Guide-Live-Attacks/blob/8f2ce944ef6d0767854d1d492cb745b1823f87d9/Images/s12d.png)

<h2>Step 12E: Secure connection between honeypot-vm and log analytics</h2>

- Under law-honeypot1 > General > Logs > search SecurityEvent and click blue Run button.
- Give it a moment, and voila! It returns the same security logs window from our honeypot-vm’s Event Viewer.
- Give it some time and search our custom: FAILED_RDP_WITH_GEO_CL will it will return our sample logs.

![image alt]()
