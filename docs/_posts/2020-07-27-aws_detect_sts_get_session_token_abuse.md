---
title: "aws detect sts get session token abuse"
excerpt: "Use Alternate Authentication Material"
categories:
  - Cloud
last_modified_at: 2020-07-27
toc: true
toc_label: ""
tags:
  - Use Alternate Authentication Material
  - Defense Evasion
  - Lateral Movement
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
---

### ⚠️ WARNING THIS IS A EXPERIMENTAL DETECTION
We have not been able to test, simulate, or build datasets for this detection. Use at your own risk. This analytic is **NOT** supported.


[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This search provides detection of suspicious use of sts:GetSessionToken. These tokens can be created on the go and used by attackers to move laterally and escalate privileges.

- **Type**: Hunting
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: 
- **Last Updated**: 2020-07-27
- **Author**: Rod Soto, Splunk
- **ID**: 85d7b35f-b8b5-4b01-916f-29b81e7a0551


#### [ATT&CK](https://attack.mitre.org/)

| ID          | Technique   | Tactic         |
| ----------- | ----------- |--------------- |
| [T1550](https://attack.mitre.org/techniques/T1550/) | Use Alternate Authentication Material | Defense Evasion, Lateral Movement |

#### Search

```
`aws_cloudwatchlogs_eks` ASIA  userIdentity.type=IAMUser
| spath eventName 
| search eventName=GetSessionToken 
| table sourceIPAddress eventTime userIdentity.arn userName userAgent user_type status region 
| `aws_detect_sts_get_session_token_abuse_filter`
```

#### Associated Analytic Story
* [AWS Cross Account Activity](/stories/aws_cross_account_activity)


#### How To Implement
You must install splunk AWS add-on and Splunk App for AWS. This search works with cloudwatch logs

#### Required field
* _time
* userIdentity.type
* eventName
* sourceIPAddress
* eventTime
* userIdentity.arn
* userName
* userAgent
* user_type
* status
* region


#### Kill Chain Phase
* Lateral Movement


#### Known False Positives
Sts:GetSessionToken can be very noisy as in certain environments numerous calls of this type can be executed. This search can be adjusted to provide specific values to identify cases of abuse. In specific environments the use of field requestParameters.serialNumber will need to be used.





#### Reference


#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)




[*source*](https://github.com/splunk/security_content/tree/develop/detections/experimental/cloud/aws_detect_sts_get_session_token_abuse.yml) \| *version*: **1**