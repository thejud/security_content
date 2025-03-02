---
title: "Excessive Number of Office Files Copied"
excerpt: "Exfiltration Over Unencrypted/Obfuscated Non-C2 Protocol"
categories:
  - Endpoint
last_modified_at: 2021-12-07
toc: true
toc_label: ""
tags:
  - Exfiltration Over Unencrypted/Obfuscated Non-C2 Protocol
  - Exfiltration
  - Splunk Behavioral Analytics
  - Endpoint_Filesystem
---

### ⚠️ WARNING THIS IS A EXPERIMENTAL DETECTION
We have not been able to test, simulate, or build datasets for this detection. Use at your own risk. This analytic is **NOT** supported.


[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This detection detects a high amount of office file copied. This can be an indicator for a malicious insider.

- **Type**: Anomaly
- **Product**: Splunk Behavioral Analytics
- **Datamodel**: [Endpoint_Filesystem](https://docs.splunk.com/Documentation/CIM/latest/User/EndpointFilesystem)
- **Last Updated**: 2021-12-07
- **Author**: Patrick Bareiss, Splunk
- **ID**: 3c6594a9-8df6-45a1-9357-d73b62083c63


#### [ATT&CK](https://attack.mitre.org/)

| ID          | Technique   | Tactic         |
| ----------- | ----------- |--------------- |
| [T1048.003](https://attack.mitre.org/techniques/T1048/003/) | Exfiltration Over Unencrypted/Obfuscated Non-C2 Protocol | Exfiltration |

#### Search

```

| from read_ssa_enriched_events() 
| eval timestamp=parse_long(ucast(map_get(input_event, "_time"), "string", null)) 
| eval action=ucast(map_get(input_event, "action"), "string", null), process=ucast(map_get(input_event, "process"), "string", null), file_name=ucast(map_get(input_event, "file_name"), "string", null), file_path=ucast(map_get(input_event, "file_path"), "string", null), dest_user_id=ucast(map_get(input_event, "dest_user_id"), "string", null), dest_device_id=ucast(map_get(input_event, "dest_device_id"), "string", null) 
| where "Endpoint_Filesystem" IN(_datamodels) 
| where action="created" 
| where like(file_name, "%.doc%") OR like(file_name, "%.xls%") OR like(file_name, "%.ppt%") 
| stats count(file_name) AS count BY dest_user_id, dest_device_id, span(timestamp, 10m) 
| where count > 20 
| eval start_time=window_start, end_time=window_end, entities=mvappend(dest_user_id, dest_device_id), body=create_map(["count", count]) 
| into write_ssa_detected_events();
```

#### Associated Analytic Story


#### How To Implement
To successfully implement this search you need to be ingesting information on process that include the name of the process responsible for the changes from your endpoints into the `Endpoint` datamodel in the `Filesytem` node.

#### Required field
* action
* process
* file_name
* file_path


#### Kill Chain Phase
* Exploitation


#### Known False Positives
user may copy a lot of office fies from one folder to another





#### Reference


#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1048.003/mass_file_creation/windows-sysmon.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1048.003/mass_file_creation/windows-sysmon.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/experimental/endpoint/excessive_number_of_office_files_copied.yml) \| *version*: **1**