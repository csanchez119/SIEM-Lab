<h1> SIEM Lab - Live RDP Brute Force</h1>
The document [SIEM Lab - Live RDP Brute Force.pdf]() includes additional pictures and visual materials.

<h2>Abstract</h2>
- This project aims to analyze and derive insights from live attack traffic to understand the risks associated with unsecured endpoints. The project employed Microsoft Azure to set up a honeypot virtual machine, establish a log analytics workspace, configure Microsoft Sentinel for security information and event management (SIEM), and visualize the collected data. PowerShell was utilized to extract data on failed Remote Desktop Protocol (RDP) logins identified in the Windows Event Viewer, which was then enriched with geolocation information via an API to pinpoint the origin of these login attempts. To maximize data collection, the virtual machine was configured with an inbound network rule to allow all traffic, and the Windows Firewall was disabled, increasing its visibility and accessibility to potential attackers. A custom log was generated and visualized in Sentinel, displaying attack attempts geographically by country, latitude, and longitude. The analysis of this data provided valuable insights into the vulnerabilities of inadequately secured endpoints and highlighted the significant risks of exposing such systems on insecure networks.
<br />


<h2> Tools and Languages </h2>

- <b>Microsoft Azure (Virtual Machines, Log Analytics Workspace, Sentinel)</b> <br />
- <b>PowerShell</b> <br />
- <b>KQL</b> <br />
- <b>ipgeolocation.io</b>


<h2> Environments </h2>

- <b> Windows 10 Pro </b>



<h2> Steps </h2>
<ol>
    <li>Set up Microsoft Azure subscription.</li>
    <li>Create a virtual machine (VM) in Azure.</li>
    <li>Configure a custom inbound network policy to allow all traffic.</li>
    <li>Create a log repository in Log Analytics Workspace.</li>
    <li>Connect the Log Analytics Workspace to the virtual machine.</li>
    <li>Set up Microsoft Sentinel for security information and event management (SIEM).</li>
    <li>Access the VM through Remote Desktop Protocol (RDP).</li>
    <li>Disable the Windows Firewall on the VM.</li>
    <li>Run a PowerShell script to extract failed RDP login data and retrieve geolocation information using ipgeolocation.io.</li>
    <li>Create a custom log in Log Analytics Workspace using the script data.</li>
    <li>Design a workbook in Microsoft Sentinel to visualize the failed RDP attempts on a map.</li>
    <li>Analyze the visualized data to assess attack patterns.</li>
</ol>


<h2> Project walk-through: </h2> 
    <ul>
        <li>
            The first step in creating the lab environment is to have an active subscription to Microsoft Azure. This can be done through a paid subscription with Azure, or in this case, I used the free trial offered by Microsoft.
        </li>
        <li>
            Once the subscription is in place, the first step is to create the virtual machine that will act as the honeypot for the exercise. Access the “Virtual Machines” service and click “Create”, selecting the “Azure virtual machine” option. In this section, I named my virtual machine, selected its location (East US 2), chose an operating system image (Windows 10 Pro), and set the admin account username and password.
        </li>
        <li>
            Before creating the virtual machine, in the “Networking” tab of the VM creation process, I made a network security group with an inbound rule allowing traffic to any destination port using any protocol. This acts as an insecure firewall allowing any internet traffic into the virtual machine. After this step, you can select review and create, and Azure will begin creating the virtual machine.
        </li>
        <li>
            Next, I created a log repository to ingest logs from the virtual machine. Using “Log Analytics Workspaces” in Azure, click “Create,” name your workspace, and add it to the same resource group as the virtual machine. I also added the workspace to the same location as the virtual machine (East US 2). Finally, review and create.
        </li>
        <li>
            After creating the log repository, I navigated to “Microsoft Defender for Cloud.” In the left panel under Management, I clicked “Environment Settings.” Here, I navigated to the log analytics workspace I created and turned off “SQL servers on machines.” Then, under Data Collection on the left panel, I selected “All Events” for collection. I then went back to “Log Analytics Workspaces,” selected the workspace I created, and in the left panel selected “Virtual Machines.” Here, I selected the honeypot VM and clicked “Connect” to link the log repository to the virtual machine.
        </li>
        <li>
            The next step was to set up the Security Information and Event Management (SIEM) using Microsoft Sentinel. In Sentinel, I selected the log analytics workspace created and added it to Sentinel. The SIEM will be used later to analyze the attack data and create a map using geolocation data.
        </li>
        <li>
            Now that there is a functional virtual machine, a repository to ingest the log data, and a SIEM for event management, it is time to remote login to the virtual machine using Remote Desktop Protocol (RDP). Keep in mind that failed remote desktop logins are used to plot the data later. Brute-forcing the login steps will generate the data used. Using the start menu, I searched for and opened the “Remote Desktop Connection” application. By entering the IP address of the VM that was created, I was prompted to enter a username and password, which are the credentials created during the VM setup. After logging in, I was met with the Windows 10 Pro interface on a new screen.
        </li>
        <li>
            Once logged into the virtual machine, it is time to shut off the Windows Firewall so the VM can respond to ICMP echo requests, making it more easily discoverable. Before shutting off the Windows Firewall, using the command line to ping the VM IP address resulted in a request timeout, indicating that the destination IP address could not communicate with other devices. To resolve this, I located <code>wf.msc</code> in the Windows start menu. Once open, I selected “Windows Defender Firewall Properties” and, for “Firewall State,” turned off the firewall for domain, private, and public profiles, then clicked “Apply.” Now that the Windows Firewall is off, using the same ping command to the VM as before, the destination IP now replies to the request.
        </li>
        <li>
            Next, I downloaded a log exporter PowerShell script from Josh Madakor's page on GitHub that pulls IP addresses from the Event Viewer. In conjunction with an IP geolocation API, it retrieves geolocation data such as country, state, city, latitude, longitude, and time zone. To function properly, I requested a new API key from the website and replaced the default key in the PowerShell script, then saved it to my virtual machine. The free version of the service only allows for 1,000 API requests, so I used the paid version for more extensive results at the end of the lab. After running the script, it created a log file and started recording any failed RDP logons (Event ID 4625) with the associated geolocation data.
            <ul>
                <li><a href="https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1" target="_blank">PowerShell Script</a></li>
                <li><a href="https://ipgeolocation.io/" target="_blank">Geolocation API</a></li>
            </ul>
        </li>
        <li>
            Now that there is a log with the geodata, we can create a custom log in Log Analytics Workspace that allows us to interact with the data. In the left side panel in the Log Analytics Workspace under “Tables,” then “Create,” I created a new custom log. Here it asks for a sample log, which trains Log Analytics on what to look for in the data. I used the sample log that was created on the VM when I first ran the PowerShell script. Next, it asks for the collection path, which is where the log is located on the VM. Since it’s running Windows 10 Pro, I selected the type as Windows and entered the exact file path (e.g., <code>C:\ProgramData</code>). In “Details,” I named the custom log <code>FAILED_RDP_GEO_CL</code> and then finished creating the log.
        </li>
        <li>
            In Microsoft Sentinel under Threat Management, I created a workbook called “Failed RDP Map.” I edited the workbook and ran a query (KQL) to extract fields from the raw data in the custom log, including timestamp, latitude, longitude, source host, state, label, destination, and country. In the visualization tab, I selected “Map.” In map settings, I could choose to plot the location info by latitude and longitude or by country. Latitude and longitude are much more precise on the map, while plotting by country provides a broader view. I also added a pie chart indicating the percentage of security events that are RDP login failures.
        </li>
        <li>
            The final results from the data collection show diversity in the attack locations and different levels of attack intensity from each place. Thousands of events plotted on the map help demonstrate the severity of the threat from each country, and the pie chart shows how common just one avenue of attack can be on a network.
        </li>
    </ul>

    

<h2> Final Results </h2>
<p>
    After a few days of monitoring the traffic, I collected over 397,000 security events on the virtual machine. From these events, 24,200 came from failed RDP logins to the machine. Over the course of the experiment, attacks were recorded from 14 different countries around the world. The biggest threat by far was Russia, with an incredible 17,900 failed logins. Russia was eight times more active than the next biggest threat, Egypt, which had 2,240 failed attempts. Russia accounted for nearly 75% of the total RDP failures. Vietnam, Mexico, the Philippines, and Ukraine were also very active, with clear signs of brute force activity. These countries all logged heavy, simultaneous traffic, indicating that the attackers were persistent and likely more sophisticated than those from countries with fewer attempts. The attackers from the US, France, Brazil, Jordan, Hong Kong, Singapore, and Morocco were not very persistent; it appears that these attackers would try a few passwords before giving up.
</p>

<p align="center">
    <img src="https://github.com/user-attachments/assets/01512ab8-695e-4c3a-bf49-7070ed0baeb0" height="85%" width="85%"/> 
</p>




<h2> Analysis and Findings </h2>

<div class="subsection">
        <h3>Importance of Password Security</h3>
        <p>
            I realized very quickly while performing this lab that password complexity is crucial for protecting an account. On my first attempt at this lab, I used a relatively weak username and password for ease of access. Within the first 30 minutes of monitoring the results, I was logged out of the virtual machine because another connection was established. This occurred several times due to a common username and an easily guessable password. It is paramount that end users practice proper password hygiene. At a minimum, users should have a password with more than 10 characters, featuring numbers, uppercase and lowercase letters, and symbols. A unique username should also be used. When reviewing the attempted usernames from each login attempt, the following appeared very frequently: Administrator, Admin, User, Username, Client, and Default.
        </p>
    </div>
    

<div class="subsection">
        <h3>Discoverability</h3>
        <p>
            Anything with an internet connection is not safe. I was shocked by how quickly the IP address of the virtual machine was discovered and even more surprised at how rapidly it could be brute-forced and accessed by an unauthorized user. Within minutes, the IP address was discovered by several countries, and once discovered, the attempts to gain access were relentless.
        </p>
    </div>

    

<div class="subsection">
        <h3>Threat Landscape</h3>
        <p>
            The results of this experiment show that threats can originate from anywhere in the world when exposed to the internet. I expected to see more traffic from areas like the US or neighboring regions, but the majority of the traffic came from distant locations such as Russia and Egypt. I also noted that some threats were more persistent than others. For instance, once Russia discovered the IP address, the login requests did not stop, a pattern also observed with Egypt, Vietnam, and Ukraine. Other threats were less concerning, such as those from Morocco, France, the United States, and Singapore. These were less alarming because there was significantly less volume compared to countries like Russia, and the attackers would stop after a few attempts. Furthermore, the login attempts from these countries were spaced out, unlike the continuous requests from more active countries. I would also like to add that the threat landscape is significantly broader than what was covered in this experiment. I only collected data from failed logins using Remote Desktop, although there are certainly other attack vectors since I allowed all traffic to any destination port. For example, Secure Shell (SSH) on port 22 or Server Message Block (SMB) on port 445 were likely heavily targeted. This means that a significant number of attacks went undetected, which could be an interesting topic for a future project.
        </p>
    </div>


<div class="subsection">
        <h3>Network Policy and Firewall</h3>
        <p>
            Setting up inbound network rules and keeping your Windows Firewall secure are the most effective ways to prevent this type of attack. Before shutting off the Windows Firewall, external communication to the computer was not possible, which alone prevents outsiders from attempting to access your computer. The inbound network rule used for this project left the computer completely vulnerable to any incoming network traffic. Ideally, risky ports like 3389 (RDP) should not be exposed to the internet because they are common targets. The same applies to other frequently targeted ports like 21 (FTP), 22 (SSH), 80 (HTTP), and 445 (SMB).
        </p>
    </div>



