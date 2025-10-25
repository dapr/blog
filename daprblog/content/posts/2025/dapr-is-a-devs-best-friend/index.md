---
date: "2025-10-23T07:00:00-07:00"
title: "Dapr is a dev's best friend"
linkTitle: "Dapr Is a Dev’s Best Friend: How to Convince Your Colleagues to Use Dapr"
author: Fraser Marlow
type: blog
---

You might see the value Dapr can bring to our organization’s development efforts, but how do you bring others along?

Dapr has evolved by leaps and bounds, so whether you are pitching it for the first time or building your case for Dapr adoption, this guide is your cheat sheet for making the case for Dapr in 2025: why engineers love it, why architects trust it, why managers back it, and why your organization can adopt it with confidence.

<center>

**Looking to make a presentation to your team?**  
Check out [the Dapr presentation deck](https://docs.dapr.io/contributing/presentations/).

</center>

## **Governance you can trust**

Dapr isn’t a vendor product—it’s a **[CNCF Graduated open-source project](https://www.cncf.io/projects/dapr/)**, placing it in the foundation’s top tier alongside Kubernetes and Prometheus. [Dapr’s graduation in November ](https://www.cncf.io/announcements/2024/11/12/cloud-native-computing-foundation-announces-dapr-graduation/)2024 signals real-world maturity and broad ecosystem backing. 

The project direction and roadmap are determined by Dapr’s elected **[Steering and Technical Committee (STC)](https://github.com/dapr/community/blob/master/steering-and-technical-committee-charter.md)**. It’s a standing governance body with published charters and elections, not a single commercial owner. That structure exists specifically to avoid over-representation by any one company and keep the roadmap community-driven. This said, the project is backed and supported by several large companies that have a large vested interest in seeing Dapr succeed.


## **The core Dapr pitch still stands: less boilerplate, more shipping**

The point isn’t gRPC or sidecars, it’s **developer velocity**. Dapr gives developers *[building blocks](https://docs.dapr.io/concepts/building-blocks-concept/)* (standard APIs) for service invocation, pub/sub, state, secrets, workflows, jobs, actors, and more, which decouples the infrastructure from the application code, so teams write business logic instead of plumbing. With the new dapr-agents framework, developers can now extend this same pattern to LLM-powered agents and workflows, letting intelligent components participate in resilient, observable service meshes without custom wiring.


* **No more boilerplate:** Store state through the Dapr API and let the platform team pick Redis, Cosmos DB, DynamoDB, etc. Swap RabbitMQ for Kafka via config, not code.

* **Polyglot by design:** Java, .NET, Python, Go, JavaScript; mix and match safely across teams.

* **Resilience and security built-in:** mTLS, retries, circuit breakers, and consistent observability patterns are part of the runtime “golden path.”


The CNCF case studies provide some real-world examples of what this means in practice:



* **[Watts Water Technologies](https://www.cncf.io/case-studies/wattswatertechnologies/)**: “We were able to stand up a completely distributed architecture in Azure in **less than one month** thanks to Dapr.” \
*By the numbers:* **1 month** to prod, **115M msgs/mo**, **100+ dev hours saved**.

* **[DataGalaxy](https://www.cncf.io/case-studies/datagalaxy/)**: “Dapr empowered us to modernize our architecture by abstracting complex infrastructure into simple building blocks.”  \
*By the numbers:* **~25M msgs/month**, **2 months** POC→prod, **130+ clusters** running Dapr.

* **[HDFC Bank](https://www.cncf.io/case-studies/hdfc-bank/)**: *Went live in ~1 year at a Tier-1 bank for UPI throttling;* *UPI timeouts decreased significantly.*



## **A “Battle-tested” framework**

Dapr is in production across industries, and CNCF publishes case studies and end-user stories you can point to in the room:

* **[DataGalaxy](https://www.cncf.io/case-studies/datagalaxy/)** (data & AI governance) on their modernization with Dapr: “**Dapr empowered us to modernize our architecture, streamline analytics, and integrate scalable AI capabilities**—effortlessly and reliably.”

* **[Vonage](https://www.cncf.io/case-studies/vonage/)** (global communications) on standardizing security and inter-service traffic: “**Dapr proved to be a phenomenal fit** … we were able to standardize tracing, AuthNZ and resilient service-to-service co mmunication.”

* **[Tempestive](https://www.cncf.io/case-studies/tempestive/) (IoT)**: “Development, implementation and management have become easier, faster and more cost-effective.” \ *By the numbers:* **~2B messages/month**, **80k+ devices**, **50% cost reduction**.

* **[HDFC Bank](https://www.cncf.io/case-studies/hdfc-bank/)**: *Handles ~**750M transactions/month**; Dapr metrics + KEDA drive horizontal scale based on HTTP traffic.*

* **[DeFacto](https://www.cncf.io/case-studies/defacto/) (retail)**: **27.5M events/100h** published, **26M/100h** consumed, **300 deployments**.


And from the Dapr end-user interview playlist:


* “**One-stop shop for pretty much anything inter-process communication.**” —[Jonas Juselius](https://www.youtube.com/watch?v=rGLh0JbSMnQ)

* “**It’s easy to focus on the business logic** … not the underlying infrastructure.” —[Florian van Dillen](https://www.youtube.com/watch?v=7rfDJHM3AVw)

* “**Component-based approach** … lets us vet different technologies without overhauling our entire stack.” —[James Fefes ](https://www.youtube.com/watch?v=UPyMxJ5mIcQ)


## **Dapr Workflows: Stable, Scalable, and Built for Real-World Automation**

With the release of **[Dapr v1.15](https://blog.dapr.io/posts/2025/02/27/dapr-v1.15-is-now-available/#dapr-workflow-stable)** earlier this year, the **Dapr Workflows** API is now **stable and production-ready**—a major milestone for developers and architects looking to automate distributed systems with confidence.  

Workflows let you orchestrate long-running, durable processes using Dapr’s familiar building blocks—**service invocation, pub/sub, state management, bindings, and secrets**—without coupling logic to infrastructure. Each workflow runs inside the Dapr runtime, which provides consistency, reliability, and visibility across every step.

### **No application is an island**

Cloud-native applications never operate in isolation. They involve multi-step processes involving data enrichment, approvals, asynchronous transactions, event-driven triggers. Each step need to run **reliably, even when things fail**. Dapr Workflows deliver this capability through a simple, language-agnostic API that developers can use in .NET, Python, Java, or Go.  

Key advantages include:

* **Durable execution:** Dapr automatically checkpoints workflow state, allowing seamless recovery after crashes, restarts, or redeployments.  
* **Cross-language orchestration:** Combine components written in different languages into a single consistent flow.  
* **Native observability:** Every workflow step integrates with Dapr’s tracing, metrics, and logs, so no extra plumbing is needed.  
* **Cloud portability:** Since workflow definitions build on Dapr APIs, you can run them on any supported environment—**Kubernetes, VM, edge, or multi-cloud**—without rewriting orchestration logic.  
* **Declarative composition:** Workflows are defined as code, so they’re versionable, testable, and fit naturally into CI/CD pipelines.  

### **Real-World Use Cases for Dapr Workflows**

Teams use Dapr Workflows today to coordinate complex processes like:

* Event-driven data pipelines and ETL jobs  
* Payment and transaction processing with compensations  
* Customer onboarding and document verification flows  
* Automated deployment or model training pipelines  
* IoT device provisioning and firmware rollout sequences  

In each of these, Dapr’s building blocks eliminate boilerplate, letting developers focus on business logic while Dapr ensures that every step is executed, retried, and observed consistently.

### **The Foundation for Composable Automation**

With workflows now stable, Dapr provides a **unified orchestration layer** for distributed applications—one that is **polyglot, fault-tolerant, and operations-friendly**. Teams can confidently model their end-to-end systems as orchestrated flows that scale with their architecture, not against it.  

Dapr Workflows are more than a feature—they’re the foundation for building **adaptive, event-driven systems** that can evolve alongside your organization’s needs.

## **Agentic Workflows: LLMs Meet Dapr**

Dapr is now **agentic-ready**, thanks to the launch of the new open-source project [**dapr-agents**](https://github.com/dapr/dapr-agents). This addition makes it easy to integrate **Large Language Models (LLMs)** and **autonomous AI agents** directly into your existing distributed systems—without sacrificing the resilience, portability, and consistency that define Dapr.

### **Resilient by Design**

Agentic systems often fail because of brittle orchestration: retries, timeouts, and dependency handling get complex fast. Dapr’s sidecar architecture and building blocks already solve these problems at scale.  
By connecting LLM-powered agents to Dapr components, you gain:

* **Reliable execution** — Let agents call workflows or services via Dapr’s service invocation with built-in retries and mTLS.  
* **Stateful reasoning** — Use the Dapr State API to persist memory, context, or chain-of-thought data between agent runs.  
* **Event-driven resilience** — Publish and subscribe to events safely, enabling agents to respond asynchronously to system triggers or other agents.

### **Flexible LLM Integration**

You’re not locked into any one model or provider. With **Dapr Agents**, you can choose your preferred LLM—OpenAI, Anthropic, Ollama, Azure OpenAI, or even local models—while Dapr manages the **plumbing**, **security**, and **observability**.  
The agent layer leverages Dapr components to standardize connectivity and monitoring, so you can iterate quickly and safely in production.

### **Composable Agent Architectures**

Agentic systems built on Dapr gain modularity by default. Combine agents with existing microservices or event-driven workflows, compose reasoning pipelines across teams and languages, and scale them elastically—without custom glue code.

In short, Dapr makes it possible to **run LLMs and autonomous agents as first-class citizens** in your distributed environment, while maintaining **resilience, flexibility, and governance**.



## **Why architects love it**


* **Standardize without straitjackets:** Enforce proven patterns across teams via runtime APIs—without dictating frameworks or languages.

* **Security by default:** Mutual TLS across services and policy-driven resiliency patterns, consistently applied.

* **Portability by construction:** Decouple app code from infra services so you can move clouds—or run multi-cloud/hybrid—without rewrites.

* **AI-native extensibility**: With Dapr Agents, architects can now design systems where intelligent agents interact with services, queues, and workflows using the same secure and portable runtime patterns.


The CNCF’s own project page now highlights [Dapr’s case studies](https://www.cncf.io/case-studies/?_sft_lf-project=dapr) and status among graduated projects—use that to underscore maturity and production credibility when stakeholders ask “Who else is running this?” Here are some examples of architectural benefits:

* **[HDFC Bank](https://www.cncf.io/case-studies/hdfc-bank/)**: mTLS, resiliency config, secrets abstraction, and **Dapr metrics + KEDA** for safe, elastic throughput on UPI traffic.
* **[Watts](https://www.cncf.io/case-studies/wattswatertechnologies/)** Water: mTLS out-of-the-box, retry policies without extra libraries, CRON via bindings. 
* **[At-Bay](https://www.cncf.io/case-studies/at-bay/)**: **Message-level tracing** via OpenTelemetry gives end-to-end visibility; thousands of lines of integration code saved.
* Avelo replaced its **entire departure control system in just two months**, thanks to modular, event-driven design enabled by Dapr:  “Dapr has given us the ability to design and build service patterns that easily incorporate NoSQL and messaging technologies without being tightly coupled to a particular implementation.”  — [Robert Lamberson, Chief Architect, Hynes & Khater](https://www.diagrid.io/case-studies/avelo-case-study)

**Why managers and execs back it: the 2025 numbers**

From the **State of Dapr 2025** report (v1.1; Mar 14, 2025):

* **49%** of surveyed teams run Dapr in production (up from 37% in 2023).
* **72%** use Dapr for mission-critical apps.
* **60%** report **30%+ developer time savings**; **96%** say Dapr saves time overall.
* **Multi-cloud adoption doubled** (12% → 20%); AWS usage +16 pts; on-prem +7 pts.
* Most-used APIs: **Pub/Sub (86%)**, **Service Invocation (78%)**, **State (74%)**; **Workflow** adoption is climbing fast, with strong intent for **Jobs** next.
* Top business benefits: **reduced boilerplate**, **faster feature delivery**, **cloud/vendor independence**, and **standardized operational patterns**.

These are exactly the levers execs care about: **time to value, operating consistency, and strategic portability**. Executive expectations around agentic applications is growing. Dapr’s support for agents ensures that application teams can adopt AI-native capabilities confidently on the same runtime they already trust.

Again, organizations that adopted Dapr will point to the benefits:

* **[Tempestive](https://www.cncf.io/case-studies/tempestive/)**: *50% reduction in operating costs* after moving to Dapr + K8s; modular, on-prem friendly. 
* **[Wortell](https://www.cncf.io/case-studies/wortell/) (security SaaS)**: “**Removed several software packages and a considerable amount of code**… offloaded infra integration and async comms to Dapr runtime.”

Download the full [State of Dapr Report 2025](https://pages.diagrid.io/download-the-state-of-dapr-2025-report) for more project insights.

## An open-source ethos with a thriving community

Dapr is thriving as an open source, vendor-independent project. Dapr is backed by a strong and active community.  The State of Dapr report mentioned above also provides some statistics around this:


* 8,000+ community members
* 4,300+ contributors (across the project)  ￼
* 25,200 GitHub stars and growing
* 1M+ image pulls / month, ~ 300,000 docs views / month

You can find more information and get involved by visiting[ dapr.io/community](https://dapr.io/community). The community hosts regular calls, which you can find livestreamed on their[ YouTube channel](https://www.youtube.com/@DaprDev). Additionally, Dapr recognizes its dedicated members through the Dapr Meteors Community program and fosters real-time interaction on the Dapr Discord. For deeper engagement or inquiries, feel free to reach out to the Dapr community managers. [Find our community resources page here](https://dapr.io/community/).


## **How to start the conversation (and hopefully win it)**


1. **Anchor on governance first**:  Lead with: *“Dapr is a CNCF Graduated project governed by an elected STC—no single vendor controls the roadmap.”* That defuses lock-in and “what if the owner changes direction?” concerns up front. [See the CNCF website](https://www.cncf.io/projects/dapr/).

2. **Use business-flavored “what ifs”:**
    * *“What if our next ten services ship **30% faster**?”*
    * *“What if we could swap Kafka ↔ RabbitMQ **via config, not code**?”*
    * *“What if moving regions/clouds **didn’t require rewrites**?”*

3. **Pick a pilot with visible pain:** A new service or a thorny cross-cutting concern (auth, pub/sub, state, workflow). Aim to replace boilerplate with Dapr APIs and measure the deltas (lead time, defect rate, on-call toil). Or, pick an AI-augmented workflow pilot (for example, a summarization or monitoring agent). It’s a fast way to showcase how Dapr extends naturally into agentic architectures.

4. **Show production voices, not slides:** Keep two or three short quotes handy (like the ones above) and link to the CNCF case study pages for credibility with legal/compliance and the PMO.

5. **Adopt incrementally:** Introduce Dapr API-by-API (e.g., start with service invocation + mTLS, then add pub/sub, then state). This lets teams feel wins quickly without a “big-bang” rewrite.

## **Quick talk tracks by persona**

* **Developer:** “You keep your language and framework; Dapr lifts the cross-cutting stuff—retries, mTLS, service discovery, pub/sub, state, workflows—behind a clean API. Less glue code, more product.”

* **Architect:** “Uniform patterns, polyglot safe, and portability without framework lock-in. Strong security defaults and consistent resiliency across services.”

* **Platform/DevOps:** “Centralized, observable runtime; configurable components; easier upgrades and policy enforcement. Fewer one-off snowflakes.”

* **Manager/Exec:** “Faster delivery, consistent operations, and freedom to run in multiple clouds or shift providers—without code churn.”


## **The TL;DR you can paste into chat or a PRD**

**Why Dapr now:** It’s a **CNCF-graduated** OSS runtime with elected governance (no single vendor), **widely used in production**, and **proven to cut delivery time** while increasing portability and resilience. **Real users** report easier modernization and “one-stop” inter-service features, and the **2025 user survey** shows rising production usage and **30%+ time savings** for most teams. 

---

### **Sources you can include in your internal doc/slide notes**

* [CNCF announcement & project page](https://www.cncf.io/announcements/2024/11/12/cloud-native-computing-foundation-announces-dapr-graduation/): Dapr Graduated (Oct–Nov 2024) and current overview.

* [Dapr governance](https://blog.dapr.io/posts/2020/09/30/transitioning-the-dapr-project-to-open-governance/): STC structure, elections, and intent to avoid single-vendor dominance.

* [CNCF case studies](https://www.cncf.io/case-studies?_sft_lf-project=dapr) (quotes & metrics): DataGalaxy, Vonage, Grafana, HDFC Bank, Wortell, and many more.

* [Dapr end-user interview playlist](https://www.youtube.com/playlist?list=PLcip_LgkYwztC29lid5mM6qcotnX0WjQL).


---
