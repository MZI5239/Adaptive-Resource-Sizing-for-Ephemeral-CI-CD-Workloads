Adaptive Resource Sizing for Ephemeral CI/CD Workloads: Mitigating Resource Over-Provisioning in Kubernetes via Machine Learning
Cutoff 1 Proposal — Day 10
 Research Area: AI-Driven Cloud Resource Optimization
Group and Roles
Group Number (from Google Sheet): G30
 Section: B
 Primary Researcher (Muhammad Zain Imran, 23i-0855, i230855@isb.nu.edu.pk):
 Co-Researcher (Taha Faisal, 23i-0592, i230592@isb.nu.edu.pk): 
Approved Research Area and Title
Research Area: AI-Driven Cloud Resource Optimization.
Working Title: Adaptive Resource Sizing for Ephemeral CI/CD Workloads: Mitigating Resource Over-Provisioning in Kubernetes via Machine Learning.
Problem and Motivation
Modern DevSecOps pipelines increasingly execute inside Kubernetes, where each pipeline stage — build, static application security testing (SAST), dynamic application security testing (DAST), and automated test execution — runs as a short-lived, ephemeral container job. These jobs typically complete within minutes, unlike the long-running microservices for which Kubernetes' native resource-management tools were designed.
Because the Vertical Pod Autoscaler (VPA) requires repeated observation of a running pod's resource consumption over time before it can recommend or apply a resize, it is structurally unable to act on jobs that terminate before a single learning cycle completes. In the absence of a viable alternative, engineering teams fall back on static, worst-case resource requests, sizing every job type to accommodate its historically largest observed run. This is operationally safe but wasteful: the same conservative request is applied uniformly, regardless of the size or nature of the underlying code change, so most pipeline runs reserve far more CPU and memory than they consume.
As organizations scale DevSecOps pipelines to run dozens of concurrent ephemeral jobs, this static over-provisioning accumulates into substantial unused ("slack") cluster capacity, directly increasing infrastructure cost and constraining the number of pipelines a cluster can run concurrently.
Initial Research Gap
Existing autoscaling literature focuses overwhelmingly on long-running, request-driven workloads such as web microservices, where autoscalers can observe sustained traffic patterns before adjusting resources. Kubernetes' native VPA explicitly targets this class of long-running services and is not designed to act on short-lived, bursty batch jobs. Because CI/CD jobs terminate before VPA can complete a learning cycle or be restarted with revised settings, they fall outside VPA's operating assumptions entirely.
This leaves a gap in the literature: there is limited published work on adaptive, pre-execution resource profiling for heterogeneous, ephemeral DevSecOps pipeline jobs, where the sizing decision must be made once, before the job starts, using only information available prior to execution — a different problem from the reactive, mid-execution resizing that dominates existing Kubernetes autoscaling research.
Primary Research Question
How effectively can an adaptive machine-learning profiling controller dynamically size CPU and memory requests for ephemeral CI/CD container jobs in Kubernetes, in order to reduce cluster resource over-provisioning (slack), without violating pipeline execution-time SLAs, compared to static worst-case allocation?
Objectives
Characterize existing resource request-versus-actual-usage patterns ("slack") for ephemeral CI/CD jobs (SAST, DAST, and automated test suites) currently running under static, worst-case Kubernetes resource allocation.
Design and implement a pre-execution machine-learning profiling controller that predicts appropriate CPU and memory requests for a CI/CD job before scheduling, using job metadata (job type, code-change size, language/toolchain) and historical execution telemetry as input features.
Integrate the controller into the Kubernetes scheduling path (e.g., as a mutating admission webhook) so that predicted resource requests can override static defaults at job submission time.
Empirically evaluate the controller against static worst-case allocation on a representative set of heterogeneous CI/CD jobs, measuring resource slack and pipeline SLA-violation rate.
Proposed Methodology
Phase 1 — Baseline characterization: Historical execution telemetry (CPU/memory requests, actual peak usage, and job duration) will be collected from a representative CI/CD pipeline (e.g., GitLab CI or Tekton) running on a test Kubernetes cluster, across a mix of job types (SAST, DAST, unit/integration test suites). This data establishes the baseline resource slack under current static allocation.
Phase 2 — Controller design and implementation: A lightweight, pre-execution ML model (e.g., a gradient-boosted regression model) will be trained to predict CPU and memory requirements from features available before a job starts, such as job type, changed-file/line count, and historical statistics for that job class. The trained model will be deployed as a Kubernetes mutating admission webhook that intercepts job-submission requests and rewrites their resource requests before scheduling.
Phase 3 — Comparative evaluation: The adaptive controller will be benchmarked against two baselines — (a) static worst-case allocation (current practice) and (b) Kubernetes VPA in its closest applicable configuration — on the same set of representative pipelines, using a repeated-run experimental design on a dedicated test cluster.
Expected Metrics and Outcomes
Resource Slack / Overrun Rate — the gap between requested and actually consumed CPU/memory per job.
Pipeline Execution-Time SLA Violation Rate — percentage of jobs exceeding an agreed completion-time threshold.
Scheduling Latency — overhead introduced by the profiling/webhook step.
Aggregate Cluster Utilization Improvement — change in overall reserved-vs-used capacity across concurrent pipeline runs.
Expected outcome: a statistically significant reduction in resource slack relative to static worst-case allocation, without a corresponding increase in SLA violations.
References
[1] R. Pachyappan, S. Gahlot, and F. Hasenkhan, "AI-Powered CI/CD Pipeline Optimization Using Reinforcement Learning in Kubernetes-Based Deployments," ESP International Journal of Advancements in Computational Technology (ESP-IJACT), vol. 3, no. 1, pp. 132–139, 2025.
[2] R. Soth, P. Vann, and L. Sok, "Kubernetes Resource Optimization Using Machine Learning: A Comprehensive Review of Widely Used Techniques," Preprints.org, 2026, doi: 10.20944/preprints202606.1718.v1. (Preprint, not yet peer-reviewed.)
[3] M. Alhakimi and R. Latip, "Enhancing the Kubernetes Scheduler: A State-of-the-Art Review from Cloud to Edge," Computers, vol. 15, no. 7, p. 458, 2026, doi: 10.3390/computers15070458.
[4] P. Liu and J. Guitart, "Fine-Grained Scheduling for Containerized HPC Workloads in Kubernetes Clusters," in Proc. IEEE 24th Int. Conf. on High Performance Computing & Communications (HPCC/DSS/SmartCity/DependSys), 2022, pp. 275–284, doi: 10.1109/HPCC-DSS-SmartCity-DependSys57074.2022.00068.
[5] W. K. Lai, Y. C. Wang, and S. C. Wei, "Delay-Aware Container Scheduling in Kubernetes," IEEE Internet of Things Journal, vol. 10, no. 13, pp. 11813–11824, 2023, doi: 10.1109/JIOT.2023.3244545.

