---
date: "2023-09-05T08:00:00-07:00"
title: "Dapr Completes 2023 Security Audit - Increasing Enterprise Confidence"
linkTitle: "Dapr Completes 2023 Security Audit - Increasing Enterprise Confidence"
author: Yaron Schneider
type: blog
---

Dapr is trusted by thousands of developers from companies of all sizes to handle their mission critical workloads. These range from manufacturing to automotive to financial services and more. Security is at the forefront of Dapr’s efforts. By interacting with cloud infrastructure such as databases, message brokers, secret stores and other types of core services, Dapr extends concepts like identity and access control to more than the network layer and point-to-point communications, providing developers with a holistic approach to application zero-trust security. Paramount to all these efforts is a well established security posture for Dapr’s codebase and architecture.

We are excited to announce that Dapr has concluded its 2023 security audit after a 3 month long joint collaboration with [ADA Logics](https://adalogics.com/), the [CNCF](https://www.cncf.io/?s=security+audit) and [OSTIF](https://ostif.org/). The full report and findings are available publicly [here](https://docs.dapr.io/docs/Dapr-september-2023-security-audit-report.pdf).

The completion of this audit marks a crucial milestone for the project and increases confidence in the project for all of Dapr’s end users, as well as setting up the project for success when it looks to reach the graduation stage in the future.

## Summary

The auditor’s assessment found that _“Our overall assessment of Dapr is highly positive. Dapr follows security best practices in both design and implementation. Dapr performed well in this audit demonstrating a strong security posture.”_

Key findings:

- 7 Issues were identified by the ADA Logics team
- None were of critical or high severity
- 4 issues were identified as moderate, 2 as low, and 1 as informational
- A CVE was identified in a 3rd party dependency that Dapr was using. The dependency was patched and fixed

The security audit was very thorough and covered the main repositories that contain the code of the Dapr runtime, including its control plane and dataplane. Of primary importance is Dapr’s [components-contrib](https://github.com/dapr/components-contrib) repository that contains the different implementations that enable Dapr to connect and integrate with a wide range of cloud infrastructure services. All issues have so far been fixed with the exception of one, classified as low severity.

The Dapr project maintainers and its steering committee are grateful for the work done by the ADA Logics team and command them for a well executed and professional review including a clear and fruitful communication process along the way.

## Highlights and learnings

### Responsibility for securing cloud infrastructure

The auditors brought to the surface an important point which is to make sure users do their best to secure the core infrastructure that Dapr connects to through the use of Dapr’s component model. While the issue found didn’t present an attack vector in Dapr itself, it highlighted how malicious users could cause an out-of-memory panic in Dapr if they were to, for example, gain access to the underlying infrastructure that Dapr is connected to and modify the data to sizes that would cause the OOM panic.

### CVE in Avro’s Golang package

It was found that an attacker could cause an OOM panic by sending a well-crafted payload due to a bug in a downstream dependency of Dapr. The issue was not caused by Dapr but by the implementation in the underlying package, which has been patched and fixed. This finding highlights the importance of tracking dependencies. According to auditors, the exploitability of this vulnerability is low as it required the use of a specific component and non-default settings, in addition to having the attacker be able to gain access to Dapr’s APIs.

### SQL strings sanitization

Dapr’s MySQL and Postgres bindings did not sanitize query parameters before sending them to the underlying client drivers. This could lead to potential SQL injection attacks. In response, the bindings have been patched and fixed to prevent this.

### Possible DoS in HTTP binding

Dapr provides users with a binding to connect to external HTTP services. It was found that if an attacker gained control of the service called by Dapr, they could return a large response that would be read all into memory and possibly cause an OOM panic to the Dapr process. This issue was patched and fixed.

### SLSA

SLSA is a security framework, a checklist of standards and controls to prevent tampering, improve integrity, and secure packages and infrastructure. Dapr does not currently generate a provenance statement. While Dapr’s build process was reviewed by auditors and found to “perform well against requirements for the build platform”, it is important for the project to be able to generate provenance artifacts, and work will be underway to enable this using the [SLSA generator](https://github.com/slsa-framework/slsa-github-generator) for GitHub Actions as recommended by the auditing team.
