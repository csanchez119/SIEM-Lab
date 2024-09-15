<h1> SIEM Lab - Live RDP Brute Force</h1>

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
