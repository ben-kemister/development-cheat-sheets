---
title: AIPerf
date: "2026-07-17"
tags: [AIPerf, LLM, Kubernetes, Benchmarking, NVIDIA, GPU]
---

AIPerf is a comprehensive benchmarking tool designed to measure the performance of generative AI models served by any preferred inference solution.
<!--more-->
AIPerf is NVIDIA's next-generation successor to GenAI-Perf, specifically engineered to handle the complexities of benchmarking 
Large Language Models (LLMs) in production environments like Kubernetes. 
It utilizes a scalable, multiprocess architecture to provide deep visibility into model performance and GPU telemetry.

### Core Functionality and Architecture

AIPerf is built on a sophisticated 9-service multiprocess architecture using ZMQ for communication between components. 
This architecture enables high scalability and supports several key benchmarking capabilities:

* **Realistic Load Generation:** Supports concurrency sweeps, request rate control, and trace replay to simulate real-world usage.
* **Extensive Metric Collection:** Measures critical LLM performance indicators, including Time to First Token (TTFT), Time to Second Token (TTST), and Inter-Token Latency (ITL).
* **GPU Telemetry Integration:** Correlates inference latency with real-time GPU metrics (utilization, memory, power, and temperature) by connecting to the NVIDIA DCGM Exporter.
* **Flexible Endpoint Support:** Compatible with any OpenAI-compatible endpoint, including vLLM, NVIDIA Triton, TGI, Ollama, and Anthropic Messages API.

### Deployment via Kubernetes

AIPerf can be deployed as a Kubernetes Job to automate benchmarks within a cluster. 
Using a Job allows for easy resource management and the ability to export results to persistent storage.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: aiperf-benchmark
  namespace: ai-inference
spec:
  backoffLimit: 0
  template:
    spec:
      restartPolicy: Never
      containers:
      - name: aiperf
        image: python:3.11-slim
        command:
        - /bin/bash
        - -c
        - |
          pip install aiperf
          aiperf profile \
            --model llama3-8b \
            --streaming \
            --endpoint-type chat \
            --url http://vllm-server.ai-inference:8000 \
            --concurrency 16 \
            --request-count 200 \
            --tokenizer meta-llama/Llama-3-8B-Instruct \
            --ui simple \
            --artifact-dir /results/llama3-c16
          echo "=== Benchmark Complete ==="
          cat /results/llama3-c16/*_aiperf.csv
        resources:
          limits:
            cpu: "4"
            memory: 8Gi
        volumeMounts:
        - name: results
          mountPath: /results
      volumes:
      - name: results
        persistentVolumeClaim:
          claimName: benchmark-results
```

### Key Performance Metrics

AIPerf provides a detailed breakdown of latency and throughput metrics to evaluate the user experience and system efficiency:

* **TTFT (Time to First Token):** Determines the perceived responsiveness of the model.
* **TTST (Time to Second Token):** Measures the overhead of KV cache allocation, providing insight into the system's readiness for streaming.
* **ITL (Inter-Token Latency):** Affects the smoothness and quality of the streaming text experience.
* **Throughput:** Reports both Output Token Throughput (tokens/sec) and Request Throughput (requests/sec).

### Best Practices for Benchmarking

To ensure accurate and reproducible results, consider the following recommendations:

* **UI Selection:** Use `--ui simple` for Kubernetes Jobs, as the interactive dashboard requires a TTY which standard jobs lack. 
    Use `--ui dashboard` only via `kubectl exec -it` for interactive debugging.
* **Reproducibility:** Always use the `--random-seed` flag to ensure benchmarks can be replicated.
* **Warmup:** Use `--warmup-request-count 10` to avoid skewed data caused by initial cold-start latency.
* **Tokenizer Accuracy:** Explicitly match the `--tokenizer` to the specific model being tested to ensure correct token counts.
* **Data Persistence:** Always use `--artifact-dir` mounted to a PersistentVolumeClaim (PVC) to prevent losing benchmark results when the Job completes.

### Handy Links

* [AIPerf GitHub Repository](https://github.com/ai-dynamo/aiperf)
* [Kubernetes Recipes: AIPerf Guide](https://kubernetes.recipes/recipes/ai/aiperf-benchmark-llm-kubernetes/)

