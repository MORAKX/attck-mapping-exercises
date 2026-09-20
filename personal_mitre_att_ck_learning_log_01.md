# My First MITRE ATT&CK® Mapping Exercise: Self-Correction & Learning Log

**Goal:** Document my initial guesses, analyze where my intuition was spot-on, and understand why certain techniques were off so I can build a sharper "matrix mindset."

---

## The Threat Scenario

> *"The threat actor logged directly into an internal Windows server over Remote Desktop Protocol (RDP) using stolen domain credentials. After gaining access, the attacker used the command-line utility `certutil.exe` to download a secondary malicious payload from an external web server. To maintain persistent access, the attacker created a new local user account and added it to the local Administrators group. Next, the attacker executed a credential-dumping tool to extract passwords directly from LSASS process memory. Finally, the attacker gathered sensitive project documents and exfiltrated them by uploading the files to an external cloud storage service (Mega.io)."*

---

## Detailed Breakdown: My Guesses vs. Real-World Mappings

### Behavior 1: RDP Login using Stolen Domain Credentials
* **My Guesses:** 
  * `T1589.001` (Gather Victim Identity Information: Credentials)
  * `T1078.002` (Valid Accounts: Domain Accounts)
  * `T1563.002` (Remote Service Session Hijacking: RDP Hijacking)
* **Ideal Primary Mapping:** `T1021.005` (Remote Services: Remote Desktop Protocol) + `T1078.002` (Valid Accounts: Domain Accounts)
* **What I got right:** I identified `T1078.002` (Domain Accounts), which is the exact right sub-technique for *how* they authenticated.
* **Why my other choices weren't quite right:**
  * **`T1589.001` vs. Active Use:** `T1589.001` lives in the **Reconnaissance** tactic phase (gathering or buying creds *before* attacking). Since the actor is actively logging in with them here, it falls under **Initial Access / Lateral Movement**.
  * **`T1563.002` vs. Normal RDP Login:** RDP Hijacking means taking over an existing, disconnected session *without needing to re-authenticate*. Because the text says they "logged directly in... using stolen credentials," it is standard **Remote Services: RDP** (`T1021.005`).

---

### Behavior 2: Downloading Payload via `certutil.exe`
* **My Guess:** `T1105` (Ingress Tool Transfer)
* **Ideal Primary Mapping:** `T1105` (Ingress Tool Transfer)
* **Self-Correction Note:** **100% correct.** Using a built-in Windows tool like `certutil.exe` to pull external files is a textbook example of `T1105` (Command & Control).

---

### Behavior 3: Creating a Local Account & Adding to Local Admins
* **My Guesses:** 
  * `T1136.001` (Create Account: Local Account)
  * `T1069.001` (Permission Groups Discovery: Local Groups)
* **Ideal Primary Mapping:** `T1136.001` (Create Account: Local Account) + `T1098` (Account Manipulation)
* **What I got right:** `T1136.001` was dead on for creating the local account.
* **Why `T1069.001` wasn't quite right:**
  * **Discovery vs. Action:** `T1069.001` is under the **Discovery** tactic (listing or querying groups to see who belongs to them).
  * Adding a user to the Administrators group actually *changes privileges*, which falls under **Persistence / Privilege Escalation** as **Account Manipulation** (`T1098`).

---

### Behavior 4: Extracting Passwords from LSASS Memory
* **My Guess:** `T1003.001` (OS Credential Dumping: LSASS Memory)
* **Ideal Primary Mapping:** `T1003.001` (OS Credential Dumping: LSASS Memory)
* **Self-Correction Note:** **100% correct.** Instantly recognized process memory harvesting under Credential Access.

---

### Behavior 5: Uploading Files to Mega.io
* **My Guess:** `T1567.002` (Exfiltration to Cloud Storage)
* **Ideal Primary Mapping:** `T1567.002` (Exfiltration to Cloud Storage)
* **Self-Correction Note:** **100% correct.** Perfect sub-technique identification under Exfiltration.

---

## Comparison & Learning Summary

| Behavior | My Guesses | Correct Primary ATT&CK ID | Key Difference / Rule to Remember |
| :--- | :--- | :--- | :--- |
| **1. RDP Login** | `T1589.001`, `T1078.002`, `T1563.002` | `T1021.005` & `T1078.002` | **Phase Check:** Recon (`T1589`) happens before the attack. Active login is Lateral Movement (`T1021.005`). |
| **2. Payload Download** | `T1105` | `T1105` | **Spot on.** Pulling binaries from outside = Ingress Tool Transfer. |
| **3. Account Creation & Privilege** | `T1136.001`, `T1069.001` | `T1136.001` & `T1098` | **Action vs. Read:** `T1069` is *viewing* groups (Discovery). *Modifying* group membership is `T1098` (Account Manipulation). |
| **4. LSASS Dumping** | `T1003.001` | `T1003.001` | **Spot on.** |
| **5. Exfiltration** | `T1567.002` | `T1567.002` | **Spot on.** |

---

## Core Rules for My Next Mapping Practice

1. **Rule of Intent (Read vs. Modify):** If an attacker is listing, searching, or checking info, it's **Discovery**. If they are adding, deleting, or altering permissions, it's **Persistence / Privilege Escalation**.
2. **Rule of Timeline:** Make sure the technique matches the right stage of the attack chain. Pre-attack gathering is **Reconnaissance**, active credential use during an intrusion is **Initial Access** or **Lateral Movement**.
3. **Primary Transport vs. Authentication:** When an attacker uses credentials over a remote protocol, map both the transport method (e.g., RDP `T1021.005`) and the credential type (e.g., Domain Account `T1078.002`).
