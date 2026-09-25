# Exercise 05: FBIjobs.gov Breach (ShinyHunters) - MITRE ATT&CK Threat Mapping Analysis

**Author:** Yehia Zakaria     
**Date:** September 25, 2026  
**Type:** Personal Training Exercise & Practice Threat Mapping  
**Framework:** MITRE ATT&CK Enterprise Framework  

---

## 1. Scenario Summary
In September 2026, the cybercriminal threat group **ShinyHunters** claimed responsibility for compromising the FBI's career portal (`FBIjobs.gov`). The attackers exploited a vulnerability in an Oracle PeopleSoft application, defaced the landing page, moved into AWS cloud storage to harvest roughly 2 TB of agent and applicant data, exfiltrated the files, and posted a public extortion notice on their dark web leak site demanding the FBI pull down a previous public advisory.

---

## 2. Step-by-Step Behavior Mapping to MITRE ATT&CK®

### Behavior 1: Exploiting the Oracle PeopleSoft Platform
* **Action:** Attackers used an unpatched vulnerability in an internet-facing web server to gain initial access.
* **Tactic:** Initial Access (`TA0001`)
* **Technique:** **Exploit Public-Facing Application** (`T1190`)
* **Learning Note:** Spot on! Any time a threat actor uses a software bug (CVE/zero-day) in a publicly accessible web application to gain a foothold, it is `T1190`.

---

### Behavior 2: Defacing the `FBIjobs.gov` Website
* **Action:** The actors altered the public-facing webpage to display attacker branding and extortion notes.
* **Tactic:** Impact (`TA0040`)
* **Technique:** **Defacement: External Defacement** (`T1491.002`)
* **Learning Note:** Spot on! Altering a public website to display custom messages or vandalized pages maps directly to `T1491.002`.

---

### Behavior 3: Searching & Harvesting Cloud HR Databases
* **Action:** The attackers discovered connected AWS cloud infrastructure and queried internal databases to collect 2 TB of sensitive records.
* **Tactics:** Discovery (`TA0007`) / Collection (`TA0009`)
* **Techniques:** **Cloud Infrastructure Discovery** (`T1580`) / **Data from Information Repositories** (`T1213`)
* **Learning Note:** `T1580` was a great guess for finding the cloud environment! The second part—querying HR databases to extract tables and records—belongs under **Collection** as `T1213`.

---

### Behavior 4: Exfiltrating 2 TB of Records
* **Action:** Stolen data was moved out of the victim's cloud infrastructure over encrypted web connections to actor-controlled servers.
* **Tactic:** Exfiltration (`TA0010`)
* **Technique:** **Exfiltration Over C2 Channel** (`T1041`)
* **Learning Note:** Cloud transfer techniques (`T1567`) apply when sending data to third-party services like Google Drive or Mega. When data flows directly over the actor's encrypted command-and-control connection, it is `T1041`.

---

### Behavior 5: Extortion & Dark Web Leak Site Demand
* **Action:** The group posted stolen data samples on their Tor leak site and issued a public demand to force a response from law enforcement.
* **Tactic:** Impact (`TA0040`)
* **Technique:** **Financial Theft / Extortion** (`T1657`)
* **Learning Note:** I got stuck here and thought of Data Staging (`T1074`). Data Staging is the prep work *before* exfiltration (gathering/zipping files in a hidden local folder). Using the stolen data on a dark web portal to pressure a victim belongs under Impact (`T1657`).

---

## 3. MITRE ATT&CK® Mapping Summary Table

| Stage | Behavior Description | MITRE ATT&CK Tactic | Technique Name | ID |
| :--- | :--- | :--- | :--- | :--- |
| **1** | Exploited RCE bug in Oracle PeopleSoft | Initial Access | Exploit Public-Facing Application | `T1190` |
| **2** | Defaced public `FBIjobs.gov` site | Impact | Defacement: External Defacement | `T1491.002` |
| **3** | Located AWS cloud & queried HR databases | Discovery / Collection | Cloud Infrastructure Discovery / Data from Information Repositories | `T1580` / `T1213` |
| **4** | Sent 2 TB over encrypted traffic to actor IP | Exfiltration | Exfiltration Over C2 Channel | `T1041` |
| **5** | Demanded retraction on dark web leak site | Impact | Financial Theft / Extortion | `T1657` |

---

## 4. Key Takeaways & Self-Correction Notes

1. **Staging vs. Exfiltration vs. Extortion:**
   * **Staging (`T1074`):** Gathering files into a temporary folder or archive *inside* the victim network.
   * **Exfiltration (`T1041`):** *Sending* those files over the network to the attacker.
   * **Extortion (`T1657`):** *Leveraging* the stolen data on a public/dark web leak site to demand money or compliance.

2. **Database Collection vs. Cloud Storage:**
   * Querying SQL/Oracle/HR databases is **Data from Information Repositories** (`T1213`).
   * Downloading raw files out of S3 buckets is **Data from Cloud Storage Object** (`T1530`).
