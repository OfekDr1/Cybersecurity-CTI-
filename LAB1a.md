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

C
--
<img width="998" height="176" alt="image" src="https://github.com/user-attachments/assets/b979d583-413b-4418-9478-728e36a518d1" />

**Whats happend?**

Adding new 3 failed password from a new IP 192.0.2.55 create another T1110-Brute Force so now we have 2 T1110-Brute Force one from 192.0.2.55 and second from 127.18.0.5

D
--
<img width="985" height="177" alt="image" src="https://github.com/user-attachments/assets/5243c36e-e863-459a-aaad-d0bf080313ea" />

**Whats happend?**

Creating new Network scan detected log from another IP create new T1046 - Network Service Discovery detection 



Simulator experiment 
==
<img width="994" height="293" alt="image" src="https://github.com/user-attachments/assets/156cec8f-2849-42e1-b1c6-b97f303a9911" />

Activity D — Simulator Activity Observations:
--
Upon launching the simulator background process, the detector dynamically parsed the continuously appended log stream and identified multiple new synthetic events. Specifically, three new source IP addresses triggered T1046 (Network Service Discovery) findings (172.18.0.31, 172.18.0.33, and 172.18.0.32) due to injected explicit scan string messages, while a new source IP (172.18.0.22) triggered a T1110 (Brute Force) finding after accumulating three SSH password failures. The total finding count expanded from the baseline of 2 up to 6 findings (2 High severity, 4 Medium severity). Unlike the static manual experiments in Activity C, the simulator introduces stochastic event generation at roughly 10-second intervals. Because the log file continuously grows and the detector rescans the entire dataset on each interval, finding counts dynamically accumulate over time rather than remaining fixed, making exact event sequencing less deterministic and reproducible.



SOC Incident Narrative & Analysis
==
Observed Facts:
--
Between 09:02 and 09:04 UTC on 2026-07-11, raw system logs recorded three sequential SSH authentication failures for user root originating from source IP 172.18.0.5 across ports 44231–44233. At 09:04:20 UTC, a log entry indicated explicit network scanning activity from IP 
172.18.0.7 targeting ports 22, 80, 443, and 3306. Subsequent automated log generation introduced additional SSH failures from IP 172.18.0.22 and scan notices from IPs 172.18.0.31, 172.18.0.32, and 172.18.0.33.   

Analyst Interpretation:
--
The activity from 172.18.0.5 and 172.18.0.22 aligns with MITRE ATT&CK technique T1110 (Brute Force), suggesting potential automated credential guessing. The activity from 172.18.0.7, 172.18.0.31, 172.18.0.32, and 172.18.0.33 corresponds to T1046 (Network Service Discovery), consistent with reconnaissance behavior. However, because detection rules rely strictly on threshold counts and hardcoded string matches, these rule triggers do not confirm successful unauthorized entry, attacker identity, malicious intent, or active compromise.
