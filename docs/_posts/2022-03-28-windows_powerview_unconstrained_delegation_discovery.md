---
title: "Windows PowerView Unconstrained Delegation Discovery"
excerpt: "Remote System Discovery
"
categories:
  - Endpoint
last_modified_at: 2022-03-28
toc: true
toc_label: ""
tags:
  - Remote System Discovery
  - Discovery
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_splunk_app_enrichmentus/cyber-security.html){: .btn .btn--success}

#### Description

The following analytic utilizes PowerShell Script Block Logging (EventCode=4104) to identify commandlets used by the PowerView hacking tool leveraged to discover Windows endpoints with Kerberos Unconstrained Delegation. Red Teams and adversaries alike may leverage use this technique for situational awareness and Active Directory Discovery.

- **Type**: [TTP](https://github.com/splunk/security_content/wiki/Detection-Analytic-Types)
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud

- **Last Updated**: 2022-03-28
- **Author**: Mauricio Velazco, Splunk
- **ID**: fbf9e47f-e531-4fea-942d-5c95af7ed4d6


#### Annotations

<details>
  <summary>ATT&CK</summary>

<div markdown="1">


| ID             | Technique        |  Tactic             |
| -------------- | ---------------- |-------------------- |
| [T1018](https://attack.mitre.org/techniques/T1018/) | Remote System Discovery | Discovery |

</div>
</details>


<details>
  <summary>Kill Chain Phase</summary>

<div markdown="1">

* Reconnaissance


</div>
</details>


<details>
  <summary>NIST</summary>

<div markdown="1">

* DE.CM



</div>
</details>

<details>
  <summary>CIS20</summary>

<div markdown="1">

* CIS 3
* CIS 5
* CIS 16



</div>
</details>

<details>
  <summary>CVE</summary>

<div markdown="1">


</div>
</details>

#### Search

```
`powershell` EventCode=4104 (Message = "*Get-DomainComputer*" OR Message = "*Get-NetComputer*") AND (Message = "*-Unconstrained*") 
| stats count min(_time) as firstTime max(_time) as lastTime by EventCode Message ComputerName User 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `windows_powerview_unconstrained_delegation_discovery_filter`
```

#### Macros
The SPL above uses the following Macros:
* [powershell](https://github.com/splunk/security_content/blob/develop/macros/powershell.yml)
* [security_content_ctime](https://github.com/splunk/security_content/blob/develop/macros/security_content_ctime.yml)

Note that **windows_powerview_unconstrained_delegation_discovery_filter** is a empty macro by default. It allows the user to filter out any results (false positives) without editing the SPL.

#### Required field
* _time
* EventCode
* Message
* ComputerName
* User


#### How To Implement
The following  analytic requires PowerShell operational logs to be imported. Modify the powershell macro as needed to match the sourcetype or add index. This analytic is specific to 4104, or PowerShell Script Block Logging.

#### Known False Positives
Administrators or power users may leverage PowerView for system management or troubleshooting.

#### Associated Analytic story
* [Active Directory Kerberos Attacks](/stories/active_directory_kerberos_attacks)




#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 35.0 | 50 | 70 | Suspicious PowerShell Get-DomainComputer was identified on endpoint $ComputerName$ |


#### Reference

* [https://attack.mitre.org/techniques/T1018/](https://attack.mitre.org/techniques/T1018/)
* [https://adsecurity.org/?p=1667](https://adsecurity.org/?p=1667)
* [https://docs.microsoft.com/en-us/defender-for-identity/cas-isp-unconstrained-kerberos](https://docs.microsoft.com/en-us/defender-for-identity/cas-isp-unconstrained-kerberos)
* [https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/domain-compromise-via-unrestricted-kerberos-delegation](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/domain-compromise-via-unrestricted-kerberos-delegation)
* [https://www.cyberark.com/resources/threat-research-blog/weakness-within-kerberos-delegation](https://www.cyberark.com/resources/threat-research-blog/weakness-within-kerberos-delegation)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [replay.py](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)


* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1018/unconstrained/windows-powershell.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1018/unconstrained/windows-powershell.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/windows_powerview_unconstrained_delegation_discovery.yml) \| *version*: **1**