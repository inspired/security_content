---
title: "Excessive Service Stop Attempt"
excerpt: "Service Stop
"
categories:
  - Endpoint
last_modified_at: 2021-05-04
toc: true
toc_label: ""
tags:
  - Service Stop
  - Impact
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
  - Endpoint
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_splunk_app_enrichmentus/cyber-security.html){: .btn .btn--success}

#### Description

This analytic identifies suspicious series of attempt to kill multiple services on a system using either `net.exe` or `sc.exe`. This technique is use by adversaries to terminate security services or other related services to continue there objective and evade detections.

- **Type**: [Anomaly](https://github.com/splunk/security_content/wiki/Detection-Analytic-Types)
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: [Endpoint](https://docs.splunk.com/Documentation/CIM/latest/User/Endpoint)- **Datasource**: [Splunk Add-on for Sysmon](https://splunkbase.splunk.com/app/5709)
- **Last Updated**: 2021-05-04
- **Author**: Teoderick Contreras, Splunk
- **ID**: ae8d3f4a-acd7-11eb-8846-acde48001122


#### Annotations

<details>
  <summary>ATT&CK</summary>

<div markdown="1">


| ID             | Technique        |  Tactic             |
| -------------- | ---------------- |-------------------- |
| [T1489](https://attack.mitre.org/techniques/T1489/) | Service Stop | Impact |

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

| tstats `security_content_summariesonly` values(Processes.process) as process values(Processes.process_id) as process_id count min(_time) as firstTime max(_time) as lastTime  from datamodel=Endpoint.Processes where `process_net` OR  Processes.process_name = "sc.exe" OR  Processes.process_name = "net1.exe" AND Processes.process="*stop*" OR Processes.process="*delete*" by Processes.process_name Processes.original_file_name Processes.parent_process_name Processes.dest Processes.user _time span=1m 
| where count >=5 
| `drop_dm_object_name(Processes)` 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `excessive_service_stop_attempt_filter`
```

#### Macros
The SPL above uses the following Macros:
* [security_content_summariesonly](https://github.com/splunk/security_content/blob/develop/macros/security_content_summariesonly.yml)
* [security_content_ctime](https://github.com/splunk/security_content/blob/develop/macros/security_content_ctime.yml)
* [process_net](https://github.com/splunk/security_content/blob/develop/macros/process_net.yml)

Note that **excessive_service_stop_attempt_filter** is a empty macro by default. It allows the user to filter out any results (false positives) without editing the SPL.

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
unknown

#### Associated Analytic story
* [XMRig](/stories/xmrig)
* [Ransomware](/stories/ransomware)




#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 80.0 | 80 | 100 | An excessive amount of $process_name$ was executed on $dest$ attempting to disable services. |


#### Reference

* [https://thedfirreport.com/2020/04/20/sqlserver-or-the-miner-in-the-basement/](https://thedfirreport.com/2020/04/20/sqlserver-or-the-miner-in-the-basement/)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [replay.py](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)


* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/malware/xmrig_miner/windows-sysmon.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/malware/xmrig_miner/windows-sysmon.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/excessive_service_stop_attempt.yml) \| *version*: **2**