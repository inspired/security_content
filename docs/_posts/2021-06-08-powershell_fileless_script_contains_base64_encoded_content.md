---
title: "Powershell Fileless Script Contains Base64 Encoded Content"
excerpt: "Command and Scripting Interpreter
, Obfuscated Files or Information
, PowerShell
"
categories:
  - Endpoint
last_modified_at: 2021-06-08
toc: true
toc_label: ""
tags:
  - Command and Scripting Interpreter
  - Obfuscated Files or Information
  - PowerShell
  - Execution
  - Defense Evasion
  - Execution
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_splunk_app_enrichmentus/cyber-security.html){: .btn .btn--success}

#### Description

The following analytic utilizes PowerShell Script Block Logging (EventCode=4104) to identify suspicious PowerShell execution. Script Block Logging captures the command sent to PowerShell, the full command to be executed. Upon enabling, logs will output to Windows event logs. Dependent upon volume, enable on critical endpoints or all. \
This analytic identifies `FromBase64String` within the script block. A typical malicious instance will include additional code. \
Command example - `[Byte[]]$var_code = [System.Convert]::FromBase64String(38uqIyMjQ6rG....` \
During triage, review parallel processes using an EDR product or 4688 events. It will be important to understand the timeline of events around this activity. Review the entire logged PowerShell script block.

- **Type**: [TTP](https://github.com/splunk/security_content/wiki/Detection-Analytic-Types)
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud

- **Last Updated**: 2021-06-08
- **Author**: Michael Haag, Splunk
- **ID**: 8acbc04c-c882-11eb-b060-acde48001122


#### Annotations

<details>
  <summary>ATT&CK</summary>

<div markdown="1">


| ID             | Technique        |  Tactic             |
| -------------- | ---------------- |-------------------- |
| [T1059](https://attack.mitre.org/techniques/T1059/) | Command and Scripting Interpreter | Execution |

| [T1027](https://attack.mitre.org/techniques/T1027/) | Obfuscated Files or Information | Defense Evasion |

| [T1059.001](https://attack.mitre.org/techniques/T1059/001/) | PowerShell | Execution |

</div>
</details>


<details>
  <summary>Kill Chain Phase</summary>

<div markdown="1">

* Exploitation


</div>
</details>


<details>
  <summary>NIST</summary>

<div markdown="1">



</div>
</details>

<details>
  <summary>CIS20</summary>

<div markdown="1">



</div>
</details>

<details>
  <summary>CVE</summary>

<div markdown="1">


</div>
</details>

#### Search

```
`powershell` EventCode=4104 Message=*frombase64string* 
| stats count min(_time) as firstTime max(_time) as lastTime by OpCode ComputerName User EventCode Message 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `powershell_fileless_script_contains_base64_encoded_content_filter`
```

#### Macros
The SPL above uses the following Macros:
* [powershell](https://github.com/splunk/security_content/blob/develop/macros/powershell.yml)
* [security_content_ctime](https://github.com/splunk/security_content/blob/develop/macros/security_content_ctime.yml)

Note that **powershell_fileless_script_contains_base64_encoded_content_filter** is a empty macro by default. It allows the user to filter out any results (false positives) without editing the SPL.

#### Required field
* _time
* Message
* OpCode
* ComputerName
* User
* EventCode


#### How To Implement
To successfully implement this analytic, you will need to enable PowerShell Script Block Logging on some or all endpoints. Additional setup here https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell#Configure_module_logging_for_PowerShell.

#### Known False Positives
False positives should be limited. Filter as needed.

#### Associated Analytic story
* [Hermetic Wiper](/stories/hermetic_wiper)
* [Malicious PowerShell](/stories/malicious_powershell)




#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 56.0 | 70 | 80 | A suspicious powershell script contains base64 command in $Message$ with EventCode $EventCode$ in host $ComputerName$ |


#### Reference

* [https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell#Configure_module_logging_for_PowerShell.](https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell#Configure_module_logging_for_PowerShell.)
* [https://blog.palantir.com/tampering-with-windows-event-tracing-background-offense-and-defense-4be7ac62ac63](https://blog.palantir.com/tampering-with-windows-event-tracing-background-offense-and-defense-4be7ac62ac63)
* [https://static1.squarespace.com/static/552092d5e4b0661088167e5c/t/59c1814829f18782e24f1fe2/1505853768977/Windows+PowerShell+Logging+Cheat+Sheet+ver+Sept+2017+v2.1.pdf](https://static1.squarespace.com/static/552092d5e4b0661088167e5c/t/59c1814829f18782e24f1fe2/1505853768977/Windows+PowerShell+Logging+Cheat+Sheet+ver+Sept+2017+v2.1.pdf)
* [https://www.crowdstrike.com/blog/investigating-powershell-command-and-script-logging/](https://www.crowdstrike.com/blog/investigating-powershell-command-and-script-logging/)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [replay.py](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)


* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1059.001/powershell_script_block_logging/windows-powershell.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1059.001/powershell_script_block_logging/windows-powershell.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/powershell_fileless_script_contains_base64_encoded_content.yml) \| *version*: **1**