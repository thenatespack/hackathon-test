Unifying every track into a single project requires treating the conference venue as a distributed reactive ecosystem, where attendance check-ins and session telemetry drive live operational and attendee-facing workflows.

The hackathon rules permit multi-track submissions, meaning an end-to-end platform using every sponsor tool genuinely enters all four $4,000 prize categories, the Merge Forward accessibility track, and the overall NVIDIA DGX grand prize.

Below is the blueprint for **"KubePulse"**—an autonomous conference operations and networking platform that integrates all five sponsor tools into a single workflow.

---

### System Architecture & Track Mapping

```
[Attendee Check-in / App / Badges]
                │
                ▼
      [API Gateway / Ingestion]
         │                 │
         │ (OpenTelemetry) │ (Semantic Profiles & Abstracts)
         ▼                 ▼
     [ Dash0 ]       [ Elasticsearch ]
(Agent0 MCP / RUM)   (Vector Matchmaking / RAG)
         │                 │
         └────────┬────────┘
                  ▼
          [ Kubernetes Engine ]
          Managed via [ Radar ]
         (AI SRE / Autoscaling)
                  ▲
                  │ (GitOps PR Verification)
          [ Sonar / Gitar ]

```

| Component | Sponsor Tool | Architectural Responsibility | Winning Demo Action |
| --- | --- | --- | --- |
| **Code Governance & PRs** | **Sonar / Gitar** ($4k Track) | Enforces strict schema validations, API contracts between ingest/search microservices, and accessibility linting rules across your Git repo. | Show Gitar automatically reviewing pull requests, flagging breaking API changes, and validating PR conventions in CI before deployment. |
| **Telemetry & Latency Tracing** | **Dash0** ($4k Track) | Collects full-stack OpenTelemetry (OTel) traces across room check-in endpoints, query latencies, and LLM token usage via OpenLIT. | Hit a "Chaos / Spike" toggle on your UI, then invoke **Dash0 Agent0 MCP** in your terminal/editor to root-cause latency bottlenecks in seconds. |
| **Cluster Health & Auto-Remediation** | **Radar** ($4k Track) | Hosts microservices on Kubernetes; tracks pod CPU/memory pressure, manages Horizontal Pod Autoscalers (HPA), and coordinates canary rollouts. | Trigger an HPA scale-up during synthetic keynote traffic; ask Radar’s **AI SRE MCP** to audit cluster health and resolve a throttled replica. |
| **Hybrid & Semantic Matching** | **Elasticsearch** ($4k Track) | Dense vector storage and BM25 search for session abstracts, speaker transcripts, attendee technical bios, and GitHub profile embeddings. | Run a natural query like *"engineers working on eBPF telemetry"* to return matched attendees with open schedule slots and quiet-space recommendations. |
| **Inclusive Navigation & Experience** | **Merge Forward** (iPad Track) | Real-time sensory routing, live Whisper talk summarization, high-contrast queue heatmaps, and low-cognitive-load navigation. | Demo the app routing an attendee away from a packed, high-decibel hallway toward an accessible, quiet-room session with live captions. |

---

### End-to-End User Flow

1. **Attendee Registration & Check-In:**
Attendees check in or log into rooms via web client or badge pings. Each event emits an OpenTelemetry span ingested directly by **Dash0**.
2. **Dynamic Semantic Matching:**
**Elasticsearch** matches attendee interests and active skills against conference speakers, session transcripts, and other checked-in engineers. If a room is full, the search engine dynamically suggests alternate sessions matching the attendee’s vector profile.
3. **Accessibility Routing:**
The **Merge Forward** UI translates crowd density and audio telemetry into accessibility indicators: identifying sensory-friendly breakout areas, low-crowd coffee stations, and accessible seating paths in real time.
4. **Autonomous Infrastructure Scaling:**
When a popular session causes an ingress spike, **Radar** surfaces resource pressure on Kubernetes pods. The team uses Radar's MCP tool directly from the command line to trigger canary scaling and verify cluster health without manual kubectl thrashing.
5. **Continuous Verification:**
Every feature and configuration change pushed during the event goes through **Gitar**, ensuring changes meet security, accessibility, and schema standards before hitting the cluster.

---

### Hackathon Demo Script (3 Minutes)

* **Minute 1: The User Experience (Elasticsearch + Merge Forward)**
Open the attendee dashboard. Run a vector query searching for a niche technical topic. Show the hybrid search results matching a speaker session and two nearby engineers. Toggle the accessibility view to show real-time quiet zones and wheelchair-accessible routing away from crowded corridors.
* **Minute 2: The Failure Injection & Dash0 Observability**
Click a hidden "Simulate Crowd Spike" button that floods the check-in gateway with synthetic traffic. The UI alerts a slowdown. Open your coding editor and query **Dash0 Agent0 MCP**:
> *"Why is the check-in endpoint timing out?"*


Show Agent0 pinpointing downstream database contention and cold starts in real time.
* **Minute 3: Radar Remediation & Gitar Audit**
Query **Radar's AI SRE MCP** to audit the cluster state and execute an automated HPA policy update to stabilize the service. Conclude by showing the GitHub commit log: open a recent PR to highlight **Gitar’s** automated review comments enforcing schema rules and verifying the code before merge.