---
title: "Linux Preload Hijack Library Calls"
excerpt: "Dynamic Linker Hijacking, Hijack Execution Flow"
categories:
  - Endpoint
last_modified_at: 2021-12-22
toc: true
toc_label: ""
tags:
  - Dynamic Linker Hijacking
  - Persistence
  - Privilege Escalation
  - Defense Evasion
  - Hijack Execution Flow
  - Persistence
  - Privilege Escalation
  - Defense Evasion
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
  - Endpoint
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This analytic is to detect a suspicious command that may hijack a library function in linux platform. This technique is commonly abuse by adversaries, malware author and red teamers to gain privileges and persist on the machine. This detection pertains to loading a dll to hijack or hook a library function of specific program using LD_PRELOAD command.

- **Type**: TTP
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: [Endpoint](https://docs.splunk.com/Documentation/CIM/latest/User/Endpoint)
- **Last Updated**: 2021-12-22
- **Author**: Teoderick Contreras, Splunk
- **ID**: cbe2ca30-631e-11ec-8670-acde48001122


#### [ATT&CK](https://attack.mitre.org/)

| ID          | Technique   | Tactic         |
| ----------- | ----------- |--------------- |
| [T1574.006](https://attack.mitre.org/techniques/T1574/006/) | Dynamic Linker Hijacking | Persistence, Privilege Escalation, Defense Evasion |

| [T1574](https://attack.mitre.org/techniques/T1574/) | Hijack Execution Flow | Persistence, Privilege Escalation, Defense Evasion |

#### Search

```

| tstats `security_content_summariesonly` count min(_time) as firstTime max(_time) as lastTime from datamodel=Endpoint.Processes where Processes.process = "*LD_PRELOAD*" by Processes.dest Processes.user Processes.parent_process_name Processes.process_name Processes.process Processes.process_id Processes.parent_process_id Processes.process_guid 
| `drop_dm_object_name(Processes)` 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `linux_preload_hijack_library_calls_filter`
```

#### Associated Analytic Story
* [Linux Privilege Escalation](/stories/linux_privilege_escalation)
* [Linux Persistence Techniques](/stories/linux_persistence_techniques)


#### How To Implement
To successfully implement this search, you need to be ingesting logs with the process name, parent process, and command-line executions from your endpoints. If you are using Sysmon, you can use the Add-on for Linux Sysmon from Splunkbase

#### Required field
* _time
* Processes.dest
* Processes.user
* Processes.parent_process_name
* Processes.process_name
* Processes.process
* Processes.process_id
* Processes.parent_process_id


#### Kill Chain Phase
* Privilege Escalation


#### Known False Positives
Administrator or network operator can execute this command. Please update the filter macros to remove false positives.


#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 64.0 | 80 | 80 | A commandline $process$ that may hijack library function on $dest$ |




#### Reference

* [https://compilepeace.medium.com/memory-malware-part-0x2-writing-userland-rootkits-via-ld-preload-30121c8343d5](https://compilepeace.medium.com/memory-malware-part-0x2-writing-userland-rootkits-via-ld-preload-30121c8343d5)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1574.006/lib_hijack/sysmon_linux.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1574.006/lib_hijack/sysmon_linux.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/linux_preload_hijack_library_calls.yml) \| *version*: **1**