---
date: "2023-06-30T07:00:00-07:00"
title: "Dapr completes fuzzing audit"
linkTitle: "dapr-fuzzing-audit"
author: AdamKorcz
type: blog
---

The Dapr project is happy to announce the completion of their fuzzing audit which added 39 fuzzers covering Dapr Runtime, Kit and Components-Contrib. The audit is part of an initiative by the CNCF to improve the security posture of open source cloud native projects [by way of fuzzing](https://www.cncf.io/blog/2022/06/28/improving-security-by-fuzzing-the-cncf-landscape/). The audit was carried out by Ada Logics in May and June 2023. The Ada Logics team is thankful for the opportunity to help improve Daprs security posture and were impressed with the low count of issues found given the large number of fuzzers created. This is a testament to the well-written and well-maintained codebase of the Dapr project. 

The full report from the audit is available [here](https://docs.dapr.io/docs/Dapr-june-2023-fuzzing-audit-report.pdf).

## Fuzzing

Fuzzing is a valuable technique for finding software bugs. In Go, this includes bugs such as nil-dereference, out-of-bounds panics, out-of-range panics, out-of-memory panics as well as logic bugs. To test your software by way of fuzzing, you need two things:

- A fuzzing engine
- A test harness

The fuzzing engine generates the input test cases that it makes available in the test harness. It is the user's job to set up the harness such that it passes the test cases on to the API that the harness is meant to test. Dapr is implemented in Go, and the auditors wrote its test suite using the native Go fuzzing engine. 

Fuzzing is an important part in keeping CNCF projects secure and robust, and it has found security vulnerabilities and reliability issues in [several other CNCF-hosted projects](https://www.cncf.io/blog/2023/04/18/cncf-fuzzing-open-source-projects-for-security-and-reliability/).

## Fuzzing Dapr
The audit added fuzzers for 3 Dapr projects: The Dapr runtime (github.com/dapr/dapr), Dapr Kit (github.com/dapr/kit) and Components-contrib. Ada Logics started the audit by integrating Dapr into [OSS-Fuzz](https://github.com/google/oss-fuzz) - Googles open source project that runs fuzzers of critical open source projects at scale. After setting up the initial integration, Ada Logics wrote 39 fuzzers and added them to Daprs OSS-Fuzz integration. Continuity is an important part of a robust fuzzing suite; Some bugs have been found after several CPU years of continuously running fuzzers. Daprs OSS-Fuzz integration ensures that its fuzzers run even after the audit has completed to keep exploring the code base. 

This ensures that the fuzzers test for hard-to-find bugs in old code, but they also test all new code changes, since OSS-Fuzz tests the latest master/main branches of the Dapr projects in scope.

Ada Logics wrote fuzzers for a number of complex and particularly exposed endpoints, some of which include:

- The Dapr Kit crypto package: Key parsing and serialization.
- Dapr Runtime HTTP/GRPC endpoints.
- Meta data decoding used extensively in Components-Contrib.
- Apache Dubbo serialization.
- Request handling for the Dapr sidecar injector.
- Raft log handling.
- Access controls

## Found issues
The fuzzers found three issues; one in a logging utility in Dapr, one in Golang itself, and one in a third party dependency of Dapr Kits crypto package.

Three issues is an impressively low number of issues which demonstrates the mature level of the code base. Despite this maturity, fuzzing was still able to contribute by finding 3 issues affecting Dapr. 

None of the issues that the fuzzers found were severe enough to require a security advisory and have been fixed ad hoc.

## Contributing
All fuzzers from the audit are open source and were added initially to [CNCF’s fuzzing repository](https://github.com/cncf/cncf-fuzzing) and work has begun [here](https://github.com/dapr/dapr/pull/6569) to migrate the fuzzers upstream. We welcome contributions to Daprs fuzzing efforts on both the CNCF-fuzzing repository and Daprs own projects.
