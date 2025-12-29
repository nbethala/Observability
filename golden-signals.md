# The Golden Metrics (a.k.a. Golden Signals)

If you only measure four things, measure these.
They describe user experience + system health with minimal noise.

1️⃣ Latency — How long does it take? (MOST IMPORTANT)
What it means

Time taken to complete a request, job, or operation from the user’s perspective.

What to measure

Always percentiles, never averages:

p50 → typical experience

p95 → degraded experience

p99 → user pain, SLO violations, outages

Production truth:
Most incidents hide in p99, not p50.

Why latency spikes

Downstream dependency slow (DB, cache, external API)

CPU throttling

GC pauses

Queue buildup

Cold starts

Network contention

How to instrument

Histograms (Prometheus, OpenTelemetry)

Per-endpoint, per-service

Separate client-side vs server-side latency

Interview-grade statement

“We alert on sustained p99 latency breaches tied to SLOs, not averages, because tail latency is what users feel first.”

2️⃣ Errors — How broken is it?
What it means

Requests that fail from the user’s perspective.

What to measure

Error rate (%)

HTTP 5xx (server errors)

HTTP 4xx (client issues – watch trends)

Retries, timeouts, failed jobs

Common mistakes

Treating all 4xx as “not our problem”

Counting retries as success

Ignoring partial failures

Why error rate matters

Even 1% errors at scale = real users impacted

Errors often correlate with latency spikes

Interview-grade statement

“We track error rates alongside latency because slow failures are often worse than fast failures.”

3️⃣ Throughput (Traffic) — How much load?
What it means

The volume of work your system is handling.

What to measure

Requests per second (RPS / QPS)

Jobs per minute

Events per second

Inference requests (MLOps)

Why it matters

Rising traffic explains rising latency

Falling traffic during incidents = silent failure

Capacity planning depends on throughput

Failure patterns

Latency ↑ while throughput stable → resource bottleneck

Throughput ↓ suddenly → upstream outage or throttling

Interview-grade statement

“Throughput gives context to latency and error metrics—without it, you can’t tell if a spike is load-driven or systemic.”

4️⃣ Saturation — How close are we to the edge?
What it means

How exhausted your resources are.

What to measure

Infrastructure:

CPU %

Memory usage

Disk I/O

Network bandwidth

Application-level (more important):

Queue depth

Thread pool usage

DB connection pool saturation

GPU memory / utilization (ML workloads)

Why saturation causes incidents

Saturation → queues → latency → timeouts → errors → retries → death spiral

Common blind spot

Teams watch CPU but ignore queues and connection pools
This is where systems actually fail.

Interview-grade statement

“Saturation metrics explain why latency degrades before errors appear.”

How the Golden Metrics Work Together (REALITY)
Normal System

Latency: stable

Errors: low

Throughput: steady

Saturation: safe headroom

Early Failure (Best Time to Act)

p99 latency rising

Throughput normal

Saturation increasing

Errors still low

👉 Scale or fix now

Active Incident

p99 latency exploding

Errors increasing

Throughput dropping

Saturation maxed out

👉 User-visible outage

Golden Metrics → SLO Mapping (Critical)
Golden Metric	Typical SLI
Latency	% of requests < X ms
Errors	Success rate
Throughput	Request volume
Saturation	Resource utilization

SLOs are built on golden metrics.

One Sentence You Should Memorize

“The golden metrics—latency, errors, throughput, and saturation—together describe user experience, system load, and failure modes in distributed systems.”
