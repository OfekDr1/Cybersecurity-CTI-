students-

1.Ofek Drori

2.David Voloshin

**Initial finding count from clean baseline: 2**

1.Source IP 172.18.0.7- T1046 Network Service Discovery- Medium security risk

2.Source IP 172.18.0.5- T1110 Brute  Force Credential access- High security risk

<img width="1919" height="693" alt="Capture" src="https://github.com/user-attachments/assets/1c1e69fe-5e4e-4368-b8d6-641555a44f9e" />

Evidence & Uncertainty Table
--
| Evidence (Raw Log / Dashboard Summary) | Rule Trigger Condition | Technique & Tactic | Uncertainty / Limitations |
| :--- | :--- | :--- | :--- |
| `2026-07-11T09:02:11 WARN Failed password for root from 172.18.0.5 port 44231 ssh2`<br>`2026-07-11T09:02:14 WARN Failed password for root from 172.18.0.5 port 44232 ssh2`<br>`2026-07-11T09:02:18 WARN Failed password for root from 172.18.0.5 port 44233 ssh2` | Counts line occurrences containing `"Failed password"` per source IP. Triggers when `count >= 3`. | **T1110 - Brute Force**<br>(Tactic: *Credential Access*) | Does not prove attacker intent, automated tooling, or whether any login attempt ultimately succeeded. It lacks a sliding time window. |
| `2026-07-11T09:04:20 INFO Network scan detected from 172.18.0.7 against ports 22,80,443,3306` | Matches log lines containing the explicit string `"network scan detected"`. | **T1046 - Network Service Discovery**<br>(Tactic: *Discovery*) | Relies entirely on pre-formatted text injected into the log rather than inferring actual network connection patterns or PCAP packet analysis. |

Experiments
==
A
--
<img width="1813" height="423" alt="1" src="https://github.com/user-attachments/assets/0dd82970-d30d-47b8-864f-6c26c23b80c3" />

**Whats happend?**

Removing one failed-password out of the three records from 127.18.0.5 erased the T1110-Brute Force mitre tactics

B
--
<img width="1829" height="315" alt="image" src="https://github.com/user-attachments/assets/e61a4802-f71e-4c9c-a7db-d085783ac0b0" />

**Whats happend?**

Replacing IP failed-password into 3 different IPs and the T1110-Brute Force mitre tactics disappeared 
