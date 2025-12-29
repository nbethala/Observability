# GPU-Specific Golden Metrics 

The classic Golden Metrics still apply — but GPU workloads amplify saturation and tail latency.

---

<img width="1536" height="1024" alt="gpu-metrics" src="https://github.com/user-attachments/assets/881e73dd-76fe-4a1b-92db-3bb9a0061f15" />


### GPU Golden Metrics Mapping

| Golden Metric  | GPU Interpretation                                 |
| -------------- | -------------------------------------------------- |
| **Latency**    | Inference latency (p50/p95/p99), queue wait time   |
| **Errors**     | Failed inferences, CUDA errors, OOM                |
| **Throughput** | Inferences/sec, batches/sec                        |
| **Saturation** | GPU compute %, memory %, SM occupancy, queue depth |



🔥 GPU Latency (MOST IMPORTANT)
What to measure

Inference request latency

Queue delay before GPU execution

End-to-end latency (API → Triton → GPU → response)

Why GPU latency spikes

GPU memory exhaustion → paging

Oversubscription (too many pods per GPU)

Bad batching configuration

CPU → GPU contention

PCIe / NVLink bottlenecks

Production truth:
GPU latency failures usually appear first in p99, not errors.

🚨 GPU Errors
What counts as an error

CUDA OOM

Failed kernel execution

Model load failures

Inference timeouts

Retry storms

Silent GPU errors often show up as latency before error rate.

🚀 GPU Throughput
What to measure

Inferences per second

Batches per second

Tokens/sec (LLMs)

GPU utilization vs throughput (efficiency)

Throughput without utilization = wasted GPU money 💸

⚠️ GPU Saturation (Where Most Teams Fail)
Infrastructure saturation

GPU compute %

GPU memory %

Tensor Core utilization

Application saturation

Triton request queue depth

Pending inference requests

Model instance occupancy

CPU may look idle while GPU is melting.

2️⃣ Overlay: Kubernetes + MLOps + Triton Inference

Here’s the mental model you should use.

Kubernetes Layer (Node & Pod)

What K8s cares about

GPU allocation

Pod scheduling

Resource isolation

Golden Metrics

GPU per node utilization

GPU memory usage

Pod GPU assignment

Pending GPU pods

Triton Inference Layer (Serving Plane)

What Triton cares about

Request flow

Batching

Model execution

Queueing

Golden Metrics

Inference latency p99

Queue delay

Inference throughput

Model instance utilization

MLOps Layer (Business Impact)

What MLOps cares about

Prediction SLA

Cost per inference

Model health

User experience

Golden Metrics

SLO compliance

Error budget burn

GPU cost efficiency

Throughput vs latency tradeoff

End-to-End Flow (This is interview gold)
User Request
   ↓
Ingress / API Gateway
   ↓
Triton Inference Server
   ↓
Inference Queue
   ↓
GPU Execution
   ↓
Response


Metrics must exist at every boundary.

3️⃣ Exact Prometheus Metric Names (REAL, USABLE)

Below are commonly used, real metrics from:

NVIDIA DCGM

Triton Inference Server

Kubernetes

🟢 GPU Metrics (DCGM Exporter)

Compute & Memory

DCGM_FI_DEV_GPU_UTIL
DCGM_FI_DEV_MEM_COPY_UTIL
DCGM_FI_DEV_FB_USED
DCGM_FI_DEV_FB_FREE


Errors

DCGM_FI_DEV_ECC_SBE_VOL
DCGM_FI_DEV_ECC_DBE_VOL
DCGM_FI_DEV_XID_ERRORS

🟣 Triton Inference Metrics

Latency

nv_inference_request_duration_us
nv_inference_queue_duration_us
nv_inference_compute_duration_us


Throughput

nv_inference_request_success
nv_inference_request_failure


Model Utilization

nv_inference_model_execution_count
nv_inference_model_load_time_us

🔵 Kubernetes Metrics (GPU-Aware)

Pod & Node

kube_pod_container_resource_requests
kube_pod_container_resource_limits
kube_node_status_allocatable


Scheduling Pressure

kube_pod_status_phase
kube_node_status_condition

🔴 Application / SLO Metrics

Latency Histogram

http_request_duration_seconds_bucket


Error Rate

http_requests_total{status=~"5.."}


Throughput

http_requests_total

How You Alert (THIS is what seniors do)

❌ Bad alert:

GPU utilization > 90%


✅ Good alerts:

p99 inference latency breaching SLO

Triton queue delay increasing + GPU saturation

Error budget burn rate exceeded

One Sentence to Memorize (Power Move)

“In GPU-backed inference platforms, p99 latency is driven by GPU saturation and queueing, not CPU metrics, which is why we alert on Triton latency and DCGM utilization rather than node-level CPU.”
