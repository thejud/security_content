---
title: "GetWmiObject User Account with PowerShell Script Block"
excerpt: "Local Account"
categories:
  - Endpoint
last_modified_at: 2021-08-23
toc: true
tags:
  - Hunting
  - T1087.001
  - Local Account
  - Discovery
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
  - Endpoint
  - Reconnaissance
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

The following analytic utilizes PowerShell Script Block Logging (EventCode=4104) to identify the execution of the `Get-WmiObject` commandlet used with specific parameters. The `Win32_UserAccount` parameter is used to return a list of all local users. Red Teams and adversaries may leverage this commandlet to enumerate users for situational awareness and Active Directory Discovery.

- **Type**: Hunting
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: [Endpoint](https://docs.splunk.com/Documentation/CIM/latest/User/Endpoint)
- **Last Updated**: 2021-08-23
- **Author**: Mauricio Velazco, Splunk
- **ID**: 640b0eda-0429-11ec-accd-acde48001122


#### ATT&CK

| ID          | Technique   | Tactic       |
| ----------- | ----------- |--------------|
| [T1087.001](https://attack.mitre.org/techniques/T1087/001/) | Local Account | Discovery |


#### Search

```
`powershell` EventCode=4104 (Message="*Get-WmiObject*" AND Message="*Win32_UserAccount*") 
| stats count min(_time) as firstTime max(_time) as lastTime by EventCode Message ComputerName User 
| `security_content_ctime(firstTime)` 
| `getwmiobject_user_account_with_powershell_script_block_filter`
```

#### Associated Analytic Story
* [Active Directory Discovery](/stories/active_directory_discovery)


#### How To Implement
To successfully implement this analytic, you will need to enable PowerShell Script Block Logging on some or all endpoints. Additional setup here https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell#Configure_module_logging_for_PowerShell.

#### Required field
* _time


#### Kill Chain Phase
* Reconnaissance


#### Known False Positives
Administrators or power users may use this PowerShell commandlet for troubleshooting.



#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 15.0 | 30 | 50 | Local user discovery enumeration using PowerShell on $dest$ by $user$ |



#### Reference

* [https://attack.mitre.org/techniques/T1087/001/](https://attack.mitre.org/techniques/T1087/001/)



#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1087.001/AD_discovery/windows-powershell.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1087.001/AD_discovery/windows-powershell.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/getwmiobject_user_account_with_powershell_script_block.yml) \| *version*: **1**