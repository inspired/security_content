---
title: "CMD Echo Pipe - Escalation"
excerpt: "Command and Scripting Interpreter
, Windows Command Shell
, Windows Service
, Create or Modify System Process
"
categories:
  - Endpoint
last_modified_at: 2021-05-20
toc: true
toc_label: ""
tags:
  - Command and Scripting Interpreter
  - Windows Command Shell
  - Windows Service
  - Create or Modify System Process
  - Execution
  - Execution
  - Persistence
  - Privilege Escalation
  - Persistence
  - Privilege Escalation
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
  - Endpoint
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_splunk_app_enrichmentus/cyber-security.html){: .btn .btn--success}

#### Description

This analytic identifies a common behavior by Cobalt Strike and other frameworks where the adversary will escalate privileges, either via `jump` (Cobalt Strike PTH) or `getsystem`, using named-pipe impersonation. A suspicious event will look like `cmd.exe /c echo 4sgryt3436 > \\.\Pipe\5erg53`.

- **Type**: [TTP](https://github.com/splunk/security_content/wiki/Detection-Analytic-Types)
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: [Endpoint](https://docs.splunk.com/Documentation/CIM/latest/User/Endpoint)- **Datasource**: [Splunk Add-on for Sysmon](https://splunkbase.splunk.com/app/5709)
- **Last Updated**: 2021-05-20
- **Author**: Michael Haag, Splunk
- **ID**: eb277ba0-b96b-11eb-b00e-acde48001122


#### Annotations

<details>
  <summary>ATT&CK</summary>

<div markdown="1">


| ID             | Technique        |  Tactic             |
| -------------- | ---------------- |-------------------- |
| [T1059](https://attack.mitre.org/techniques/T1059/) | Command and Scripting Interpreter | Execution |

| [T1059.003](https://attack.mitre.org/techniques/T1059/003/) | Windows Command Shell | Execution |

| [T1543.003](https://attack.mitre.org/techniques/T1543/003/) | Windows Service | Persistence, Privilege Escalation |

| [T1543](https://attack.mitre.org/techniques/T1543/) | Create or Modify System Process | Persistence, Privilege Escalation |

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

| tstats `security_content_summariesonly` count min(_time) as firstTime max(_time) as lastTime from datamodel=Endpoint.Processes where `process_cmd` OR Processes.process=*%comspec%* (Processes.process=*echo* AND Processes.process=*pipe*) by Processes.dest Processes.user Processes.parent_process Processes.process_name Processes.original_file_name Processes.process Processes.process_id Processes.parent_process_id 
| `drop_dm_object_name(Processes)` 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `cmd_echo_pipe___escalation_filter`
```

#### Macros
The SPL above uses the following Macros:
* [security_content_summariesonly](https://github.com/splunk/security_content/blob/develop/macros/security_content_summariesonly.yml)
* [security_content_ctime](https://github.com/splunk/security_content/blob/develop/macros/security_content_ctime.yml)
* [process_cmd](https://github.com/splunk/security_content/blob/develop/macros/process_cmd.yml)

Note that **cmd_echo_pipe_-_escalation_filter** is a empty macro by default. It allows the user to filter out any results (false positives) without editing the SPL.

#### Required field
* _time
* Processes.dest
* Processes.user
* Processes.parent_process_name
* Processes.parent_process
* Processes.original_file_name
* Processes.process_name
* Processes.process
* Processes.process_id
* Processes.parent_process_path
* Processes.process_path
* Processes.parent_process_id


#### How To Implement
To successfully implement this search you need to be ingesting information on process that include the name of the process responsible for the changes from your endpoints into the `Endpoint` datamodel in the `Processes` node. In addition, confirm the latest CIM App 4.20 or higher is installed and the latest TA for the endpoint product.

#### Known False Positives
Unknown. It is possible filtering may be required to ensure fidelity.

#### Associated Analytic story
* [Cobalt Strike](/stories/cobalt_strike)




#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 64.0 | 80 | 80 | An instance of $parent_process_name$ spawning $process_name$ was identified on endpoint $dest$ by user $user$ potentially performing privilege escalation using named pipes related to Cobalt Strike and other frameworks. |


#### Reference

* [https://redcanary.com/threat-detection-report/threats/cobalt-strike/](https://redcanary.com/threat-detection-report/threats/cobalt-strike/)
* [https://github.com/rapid7/meterpreter/blob/master/source/extensions/priv/server/elevate/namedpipe.c](https://github.com/rapid7/meterpreter/blob/master/source/extensions/priv/server/elevate/namedpipe.c)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [replay.py](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)


* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1055/cobalt_strike/windows-sysmon.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1055/cobalt_strike/windows-sysmon.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/cmd_echo_pipe___escalation.yml) \| *version*: **2**