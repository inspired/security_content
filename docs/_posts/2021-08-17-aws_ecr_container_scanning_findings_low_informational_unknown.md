---
title: "AWS ECR Container Scanning Findings Low Informational Unknown"
excerpt: "Malicious Image
, User Execution
"
categories:
  - Cloud
last_modified_at: 2021-08-17
toc: true
toc_label: ""
tags:
  - Malicious Image
  - User Execution
  - Execution
  - Execution
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_splunk_app_enrichmentus/cyber-security.html){: .btn .btn--success}

#### Description

This search looks for AWS CloudTrail events from AWS Elastic Container Service (ECR). You need to activate image scanning in order to get the event DescribeImageScanFindings with the results.

- **Type**: [Hunting](https://github.com/splunk/security_content/wiki/Detection-Analytic-Types)
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud

- **Last Updated**: 2021-08-17
- **Author**: Patrick Bareiss, Splunk
- **ID**: cbc95e44-7c22-443f-88fd-0424478f5589


#### Annotations

<details>
  <summary>ATT&CK</summary>

<div markdown="1">


| ID             | Technique        |  Tactic             |
| -------------- | ---------------- |-------------------- |
| [T1204.003](https://attack.mitre.org/techniques/T1204/003/) | Malicious Image | Execution |

| [T1204](https://attack.mitre.org/techniques/T1204/) | User Execution | Execution |

</div>
</details>


<details>
  <summary>Kill Chain Phase</summary>

<div markdown="1">

* Actions on Objectives


</div>
</details>


<details>
  <summary>NIST</summary>

<div markdown="1">

* PR.DS
* PR.AC
* DE.CM



</div>
</details>

<details>
  <summary>CIS20</summary>

<div markdown="1">

* CIS 13



</div>
</details>

<details>
  <summary>CVE</summary>

<div markdown="1">


</div>
</details>

#### Search

```
`cloudtrail` eventSource=ecr.amazonaws.com eventName=DescribeImageScanFindings 
| spath path=responseElements.imageScanFindings.findings{} output=findings 
| mvexpand findings 
| spath input=findings
| search severity IN (LOW, INFORMATIONAL, UNKNWON) 
| rename name as finding_name, description as finding_description, requestParameters.imageId.imageDigest as imageDigest, requestParameters.repositoryName as repositoryName 
| eval finding = finding_name.", ".finding_description 
| eval phase="release" 
| eval severity="low" 
| stats min(_time) as firstTime max(_time) as lastTime by awsRegion, eventName, eventSource, imageDigest, repositoryName, user, userName, src_ip, finding, phase, severity 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `aws_ecr_container_scanning_findings_low_informational_unknown_filter`
```

#### Macros
The SPL above uses the following Macros:
* [security_content_ctime](https://github.com/splunk/security_content/blob/develop/macros/security_content_ctime.yml)
* [cloudtrail](https://github.com/splunk/security_content/blob/develop/macros/cloudtrail.yml)

Note that **aws_ecr_container_scanning_findings_low_informational_unknown_filter** is a empty macro by default. It allows the user to filter out any results (false positives) without editing the SPL.

#### Required field
* eventSource
* eventName
* responseElements.imageScanFindings.findings{}
* awsRegion
* requestParameters.imageId.imageDigest
* requestParameters.repositoryName
* user
* userName
* src_ip


#### How To Implement
You must install splunk AWS add on and Splunk App for AWS. This search works with AWS CloudTrail logs.

#### Known False Positives
unknown

#### Associated Analytic story
* [Dev Sec Ops](/stories/dev_sec_ops)




#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 7.0 | 10 | 70 | Vulnerabilities with severity high found in repository $repositoryName$ |


#### Reference

* [https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning.html](https://docs.aws.amazon.com/AmazonECR/latest/userguide/image-scanning.html)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [replay.py](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/cloud/aws_ecr_container_scanning_findings_low_informational_unknown.yml) \| *version*: **1**