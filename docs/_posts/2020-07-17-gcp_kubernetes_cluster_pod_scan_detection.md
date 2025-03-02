---
title: "GCP Kubernetes cluster pod scan detection"
excerpt: "Cloud Service Discovery"
categories:
  - Cloud
last_modified_at: 2020-07-17
toc: true
toc_label: ""
tags:
  - Cloud Service Discovery
  - Discovery
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
---

### ⚠️ WARNING THIS IS A EXPERIMENTAL DETECTION
We have not been able to test, simulate, or build datasets for this detection. Use at your own risk. This analytic is **NOT** supported.


[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This search provides information of unauthenticated requests via user agent, and authentication data against Kubernetes cluster&#39;s pods

- **Type**: Hunting
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: 
- **Last Updated**: 2020-07-17
- **Author**: Rod Soto, Splunk
- **ID**: 19b53215-4a16-405b-8087-9e6acf619842


#### [ATT&CK](https://attack.mitre.org/)

| ID          | Technique   | Tactic         |
| ----------- | ----------- |--------------- |
| [T1526](https://attack.mitre.org/techniques/T1526/) | Cloud Service Discovery | Discovery |

#### Search

```
`google_gcp_pubsub_message` category=kube-audit 
|spath input=properties.log 
|search responseStatus.code=401 
|table sourceIPs{} userAgent verb requestURI responseStatus.reason properties.pod 
| `gcp_kubernetes_cluster_pod_scan_detection_filter`
```

#### Associated Analytic Story
* [Kubernetes Scanning Activity](/stories/kubernetes_scanning_activity)


#### How To Implement
You must install the GCP App for Splunk (version 2.0.0 or later), then configure stackdriver and set a Pub/Sub subscription to be imported to Splunk.

#### Required field
* _time
* category
* responseStatus.code
* sourceIPs{}
* userAgent
* verb
* requestURI
* responseStatus.reason
* properties.pod


#### Kill Chain Phase
* Reconnaissance


#### Known False Positives
Not all unauthenticated requests are malicious, but frequency, User Agent, source IPs and pods  will provide context.





#### Reference


#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)




[*source*](https://github.com/splunk/security_content/tree/develop/detections/experimental/cloud/gcp_kubernetes_cluster_pod_scan_detection.yml) \| *version*: **1**