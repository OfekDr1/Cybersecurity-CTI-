Group members:
1.Ofek Drori
2.David Voloshin

CTI report link:
https://cloud.google.com/blog/topics/threat-intelligence/shinyhunters-renewed-mass-exploitation-campaign-targeting-oracle-peoplesoft

The summary:
The threat actor group ShinyHunters (UNC6240) launched a renewed, mass-exploitation campaign targeting organizations running Oracle PeopleSoft software.
 The attacks leverage a modified exploit for CVE-2026-35273—a critical remote code execution vulnerability in the Environment Management Hub (PSEMHUB) servlet.
 Specifically, the hackers bypassed traditional string-based Web Application Firewall (WAF) rules by using simple URL encoding (such as request paths like /%50SEMHUB/) to trick security filters.
 Once inside unpatched environments, the actors deploy persistent JSP web shells and advanced toolkits like the SIDEEYE backdoor to harvest credentials and establish command-and-control access.
 This campaign specifically targets enterprises that relied solely on perimeter WAF rules rather than applying official software patches.
 The ultimate objective of the campaign is extensive data theft, followed by extortion and threats to leak stolen corporate data.
 
 ## ShinyHunters Oracle PeopleSoft Attack Lifecycle

```mermaid
sequenceDiagram
    autonumber
    actor Attacker as Threat Actor (ShinyHunters / UNC6240)
    participant WAF as Perimeter Web Application Firewall
    participant PS as PeopleSoft Environment (PSEMHUB Servlet)
    participant FS as Application Server File System / Web Root
    participant Internal as Internal Infrastructure & Database

    Note over Attacker, Internal: Phase 1: Reconnaissance & WAF Bypass
    Attacker->>WAF: HTTP request with URL encoding (e.g., /%50SEMHUB/)
    Note right of WAF: WAF fails signature check<br/>due to path obfuscation
    WAF->>PS: Forwards obfuscated request to servlet
    PS-->>Attacker: Responds, confirming vulnerable endpoint (CVE-2026-35273)

    Note over Attacker, Internal: Phase 2: Exploitation & Web Shell Deployment
    Attacker->>PS: Sends crafted payload via HTTP POST request
    PS->>FS: Writes persistent JSP web shells (e.g., x.jsp, u.jsp)
    Attacker->>PS: Uploads advanced toolkits in chunks (SIDEEYE backdoor / Neo-reGeorg)
    PS-->>Attacker: Execution acknowledged via web shell interface

    Note over Attacker, Internal: Phase 3: Host Discovery & Credential Harvesting
    Attacker->>PS: Executes system discovery commands (whoami, tasklist)
    PS->>FS: Reads configuration and credential files (e.g., psappsrv.cfg)
    FS-->>Attacker: Returns database connections and plaintext service credentials
    Attacker->>Internal: Deploys disguised remote management agents (MeshCentral)

    Note over Attacker, Internal: Phase 4: Lateral Movement & Extortion
    Attacker->>Internal: Runs automated credential-spraying scripts (_fanout.sh via sshpass)
    Internal->>Internal: Accesses adjacent internal nodes and administrative shares
    Attacker->>Internal: Compresses and exfiltrates corporate HR, payroll, and student data
    Attacker->>Attacker: Initiates public extortion demands and data leak threats
```

### MITRE ATT&CK Mapping

| Tactic | Description | Technique |
| :--- | :--- | :--- |
| Reconnaissance | Gathering preliminary information about the target and its infrastructure to plan the attack | T1596.003 Search Open Technical Databases: Digital Certificates |
| Reconnaissance | Gathering preliminary information about the target and its infrastructure to plan the attack | T1596.005 Search Open Technical Databases: Scan Databases |
| Reconnaissance | Gathering preliminary information about the target and its infrastructure to plan the attack | T1595.002 Active Scanning: Vulnerability Scanning |
| Initial Access | Gaining an initial foothold within the target network | T1190 Exploit Public-Facing Application |
| Defense Evasion | Hiding or disguising malicious activity to bypass security controls | T1027 Obfuscated Files or Information |
| Execution | Running malicious code or commands on the targeted system | T1059.003 Command and Scripting Interpreter: Windows Command Shell |
| Execution | Running malicious code or commands on the targeted system | T1059.004 Command and Scripting Interpreter: Unix Shell |
| Persistence | Maintaining long-term access to the system across restarts | T1505.003 Server Software Component: Web Shell |
| Discovery | Exploring and mapping the internal server environment, files, and configurations | T1082 System Information Discovery |
| Discovery | Exploring and mapping the internal server environment, files, and configurations | T1016 System Network Configuration Discovery |
| Credential Access | Stealing passwords and access tokens to expand control across systems | T1552.001 Unsecured Credentials: Credentials In Files |
| Command and Control | Communicating with and managing compromised systems via attacker servers | T1090 Proxy |
| Command and Control | Communicating with and managing compromised systems via attacker servers | T1219 Remote Access Software |
| Exfiltration | Stealing and transferring sensitive data out of the target network | T1048 Exfiltration Over Alternative Protocol |

