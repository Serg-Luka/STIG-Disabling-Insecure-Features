# 🛠️ Disabling Insecure Features

In this lab, I’ll run a compliance scan using Tenable’s vulnerability management tool on a Windows 10 Pro virtual machine. The goal is to identify and disable insecure system features that could weaken the machine’s security posture.

Once the scan highlights any insecure features, I’ll manually disable them through Group Policy, the Windows Registry, or optional features settings. After applying these changes, I’ll re-run the scan to confirm the insecure features have been successfully removed or mitigated.

# 🌟 Table of Contents 🌟

- [🧰 **Tools Used**](#tools-used)
- [1. 🔍 **Configuring Compliance Scan**](#configuring-compliance-scan)
- [2. 📊 **Analysing Scan Results**](#analysing-scan-results)
- [3. 🔐 **Security Control WN10-SO-000010 Overview**](#security-control-wn10-so-000010-overview)
  - [3.1 Remediating WN10-SO-000010](#remediating-wn10-so-000010)
  - [3.2 Confirming WN10-SO-000010 Remediation](#confirming-wn10-so-000010-remediation)
- [4. 📁 **Security Control WN10-CC-000190 Overview**](#security-control-wn10-cc-000190-overview)
  - [4.1 Remediating WN10-CC-000190](#remediating-wn10-cc-000190)
  - [4.2 Confirming WN10-CC-000190 Remediation](#confirming-wn10-cc-000190-remediation)
- [5. 🔑 **Security Control WN10-CC-000040 Overview**](#security-control-wn10-cc-000040-overview)
  - [5.1 Remediating WN10-CC-000040](#remediating-wn10-cc-000040)
  - [5.2 Confirming WN10-CC-000040 Remediation](#confirming-wn10-cc-000040-remediation)
- [6. 🏁 **Lab Conclusion and Lessons Learned**](#lab-conclusion-and-lessons-learned)
  - [6.1 Conclusion](#conclusion)
  - [6.2 Lessons Learned](#lessons-learned)

<a id="tools-used"></a>
## 🧰 Tools Used

• Tenable Vulnerability Management<br>
• Windows 10 Pro VM (Azure)<br>
• Group Policy Editor<br>
• PowerShell<br>

<a id="configuring-compliance-scan"></a>
## 🔍 1.Configuring Compliance Scan

<img src="https://i.imgur.com/kMALt0q.png">

I set up an Advanced Network Scan in Tenable, specifying the private IP of my Windows 10 Pro VM as the target. I selected the internal scanner and supplied valid Windows credentials to allow authenticated scanning. In the Compliance tab, I turned on policy compliance checks, and under Plugins, I enabled the “Windows Compliance Checks” plugin. This setup enabled the scan to effectively evaluate the system’s security and configuration against defined policy standards.

<a id="analysing-scan-results"></a>
## 📊 2.Analysing Scan Results

<img src="https://i.imgur.com/vxjXQCl.png">

The scan identified several failed compliance checks on the Windows 10 Pro VM, which was a freshly deployed system on Azure. These failures are expected, as the default configuration often includes legacy or insecure features.

For this lab, I will focus on addressing insecure feature-related compliance findings to harden the system. Specifically, I will remediate the following failed checks:

•	**WN10-SO-000010** – The built-in guest account must be disabled<br>
•	**WN10-CC-000190** – Autoplay must be disabled for all drives<br>
•	**WN10-CC-000040** – Insecure logons to an SMB server must be disabled<br>

These settings introduce unnecessary security risks if left enabled. By disabling them, I will reduce the system’s attack surface—removing unauthenticated account access, preventing automatic execution from removable media, and blocking insecure SMB logon behaviour. This work supports the goal of disabling insecure features and strengthening the overall security posture.

<a id="security-control-wn10-so-000010-overview"></a>
## 🔐 3.Security Control WN10-SO-000010 Overview

<img src="https://i.imgur.com/bRsJi8V.png">

**What is WN10-SO-000010?**<br>
It’s a security rule that requires the built-in Guest account to be disabled on Windows 10 systems.

**Why remediate?**<br>
The Guest account is a well-known, default account that exists on all Windows systems and cannot be deleted. By default, it has no password and minimal restrictions, making it a prime target for unauthorised access. If left enabled, it poses a significant security risk by allowing potential attackers or unauthorised users to access the system with minimal accountability.

Disabling this account removes a low-hanging entry point and helps enforce proper identity and access controls across the system.

**Real-world example:**<br>
In many environments, attackers have used the Guest account to move laterally through networks undetected—especially in systems where audit logging is weak or not monitored. Leaving it enabled increases the chance of privilege misuse, as it offers a pre-existing entry point that’s easy to exploit. Disabling it is a simple but critical step in reducing exposure to insider threats and external attacks.

**This compliance control applies to the following key frameworks:**

•	**NIST 800-53** – Identification and authentication policies (IA-8)<br>
•	**ISO/IEC 27001:2022** – Identity management and authentication (A.5.16)<br>
•	**HIPAA** – Access control and authentication safeguards (164.312(a)(2)(i), 164.312(d))<br>
•	**GDPR** – Security of processing (Article 32.1.b)<br>
•	**DISA STIG (Windows 10)** – Secure logon enforcement (WN10-SO-000010)<br>
•	**NIST CSF (v1.1 & 2.0)** – Identity and access management (PR.AC-1, PR.AA-01)<br>

---

<a id="remediating-wn10-so-000010"></a>
### 3.1 Remediating WN10-SO-000010

<img src="https://i.imgur.com/daClW2P.png">

I opened the Local Security Policy by pressing Win + R, typing secpol.msc, and pressing Enter. Then I navigated to Local Policies > Security Options, located the Accounts: Guest account status setting, and set it to Disabled to ensure the built-in Guest account is turned off.

---

<a id="confirming-wn10-so-000010-remediation"></a>
### 3.2 Confirming WN10-SO-000010 Remediation

<img src="https://i.imgur.com/Xu6IjNF.png">

After running another compliance scan, I confirmed that WN10-SO-000010 has been successfully implemented and passed the check.

<a id="security-control-wn10-cc-000190-overview"></a>
## 📁 4. Security Control WN10-CC-000190 Overview

<img src="https://i.imgur.com/9bnzeur.png">

**What is WN10-CC-000190?**<br>
It’s a security rule that requires disabling Autoplay on all drives in Windows 10 systems.

**Why remediate?**<br>
If Autoplay is enabled, it automatically runs programs or media files as soon as a drive (like a USB stick or CD) is inserted. This behaviour can introduce malicious code without the user’s knowledge. Although Autoplay is disabled by default on some removable drives, it’s often still enabled on others, like CD-ROM or network drives.

Disabling Autoplay on all drives prevents automatic execution of potentially harmful software, reducing the risk of malware infections from removable media or network shares.

**Real-world example:**<br>
Attackers frequently use USB drives or infected CDs to spread malware via Autoplay. In many cases, malware has executed immediately after media insertion, bypassing user interaction and security controls. Disabling Autoplay has been a key step in stopping these infection vectors, making systems more resilient to this common attack method.

**This compliance control covers key frameworks including:**

•	**NIST 800-53** – Configuration management and change control (CM-7(2))<br>
•	**NIST 800-171** – System component monitoring (3.4.7, 03.04.06)<br>
•	**NIST CSF (v1.1 & 2.0)** – Protective processes and system monitoring (PR.IP-1, PR.PT-3, PR.PS-01)<br>
•	**HIPAA** – Security management (164.306(a)(1))<br>
•	**GDPR** – Security of processing personal data (Article 32.1.b)<br>
•	**DISA STIG (Windows 10)** – Configuration compliance (WN10-CC-000190)<br>
•	**PCI-DSS** – System configuration and change control (2.2.2)<br>

---

<a id="remediating-wn10-cc-000190"></a>
### 4.1 Remediating WN10-CC-000190

<img src="https://i.imgur.com/2GKEoQC.png">

I opened the Group Policy Editor by pressing Win + R, typing gpedit.msc, and hitting Enter. Then I navigated to Computer Configuration > Administrative Templates > Windows Components > AutoPlay Policies, found the Turn off AutoPlay setting, and set it to Enabled with the option for All Drives selected. This disabled Autoplay on every drive to prevent any automatic execution of media when inserted.

<img src="https://i.imgur.com/COLDgq2.png">

Alternatively, I opened an elevated PowerShell window and ran a registry command to disable Autoplay on all drives. This method updates the system directly, preventing automatic execution from any inserted media and enhancing security without using the Group Policy Editor.

---

<a id="confirming-wn10-cc-000190-remediation"></a>
### 4.2	Confirming WN10-CC-000190 Remediation

<img src="https://i.imgur.com/m60IAvS.png">

After running another compliance scan, I confirmed that WN10-CC-000190 has been successfully implemented and passed the check.

<a id="security-control-wn10-cc-000040-overview"></a>
## 🔑 5. Security Control WN10-CC-000040 Overview

<img src="https://i.imgur.com/qP9a0fD.png">

**What is WN10-CC-000040?**<br>
It’s a security rule that requires disabling insecure (unauthenticated) guest logons to SMB servers on Windows 10.

**Why remediate?**<br>
Allowing guest logons means users can access shared folders without providing a username or password. This creates a serious security risk, as anyone on the network could potentially view or modify sensitive data without any trace or control. Disabling insecure guest logons enforces proper authentication, ensuring only authorised users can access shared resources.

**Real-world example:**<br>
In many internal network breaches, attackers move laterally by probing for open shares with guest access. If guest logons are allowed, they can access data or drop malware without needing credentials. Organisations have traced data leaks and ransomware deployments back to unauthenticated SMB access. Disabling this feature closes off that attack path and strengthens access control.


**This compliance control maps to the following areas:**

•	**NIST 800-171** – Establishing system configuration baselines (3.4.2, 03.04.02a)<br>
•	**NIST 800-53 / R5** – Configuration management policy enforcement (CM-6b)<br>
•	**NIST CSF (v1.1 & 2.0)** – Configuration management and monitoring (PR.IP-1, DE.CM-09, PR.PS-01)<br>
•	**ISO/IEC 27001:2022** – Asset management and configuration (A.8.9)<br>
•	**HIPAA** – Security standard and access control (164.306(a)(1))<br>
•	**GDPR** – Protection of personal data (Article 32.1.b)<br>
•	**DISA STIG (Windows 10)** – Ensuring secure system configuration (WN10-CC-000040)<br>
•	**SWIFT-CSC** – Secure configuration practices (2.3)<br>

---

<a id="remediating-wn10-cc-000040"></a>
### 5.1 Remediating WN10-CC-000040

<img src="https://i.imgur.com/oZfjyqp.png">

To fix this, I opened the Group Policy Editor by pressing Win + R, typing gpedit.msc, and hitting Enter. Then I navigated to Computer Configuration > Administrative Templates > Network > Lanman Workstation. There, I found the policy called Enable insecure guest logons, opened it, and set it to Disabled to prevent unauthenticated access to shared folders. After clicking Apply and OK, the setting was in place to help secure SMB connections.

---

<a id="confirming-wn10-cc-000040-remediation"></a>
### 5.2	Confirming WN10-CC-000040 Remediation

<img src="https://i.imgur.com/ttB3gmf.png">

After running another compliance scan, I confirmed that WN10-CC-000040 has been successfully implemented and passed the check.

<a id="lab-conclusion-and-lessons-learned"></a>
## 🏁 6. Lab Conclusion and Lessons Learned

<a id="conclusion"></a>
### 6.1 Conclusion

In this lab, I successfully conducted a compliance scan on a Windows 10 Pro virtual machine using Tenable’s Vulnerability Management tool to identify and remediate insecure system features. By focusing on three critical compliance failures—WN10-SO-000010 (Guest account enabled), WN10-CC-000190 (Autoplay enabled), and WN10-CC-000040 (insecure SMB guest logons)—I was able to harden the system’s security posture. Each remediation was applied through either Group Policy Editor, PowerShell, or Local Security Policy, and subsequent scans confirmed that all targeted vulnerabilities were resolved. This process not only mitigated immediate risks but also aligned the system with key security frameworks such as NIST 800-53, ISO/IEC 27001, and DISA STIG. By disabling these insecure features, I reduced the system’s attack surface, ensuring better protection against unauthorised access, malware execution, and lateral movement within a network.

---

<a id="lessons-learned"></a>
### 6.2 Lessons Learned

This lab provided valuable insights into system hardening and compliance scanning. Firstly, I learned the importance of authenticated scanning with tools like Tenable, as it provides a comprehensive view of a system’s configuration weaknesses, which unauthenticated scans might miss. Secondly, I gained a deeper understanding of how default Windows configurations, such as enabled Guest accounts or Autoplay, can introduce significant vulnerabilities if left unaddressed. The remediation process highlighted the effectiveness of Group Policy and registry edits as precise tools for enforcing security controls, though I noted that PowerShell offers a faster, scriptable alternative for certain tasks. Additionally, I recognised the need to balance security with usability—disabling features like Autoplay or SMB guest logons must be carefully considered in environments where legitimate use cases exist. Finally, mapping each remediation to compliance frameworks underscored the importance of aligning technical controls with organisational and regulatory requirements, ensuring both security and audit readiness. This experience has equipped me with practical skills to proactively identify and mitigate insecure features in future system hardening efforts.
