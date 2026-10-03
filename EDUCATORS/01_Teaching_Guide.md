# KAZCADE_RUNTIME — Educator's Teaching Guide

## Course Fit: runtime systems, model serving, MLOps, performance engineering

## 3-Week Module: Low-Latency AI Runtime Engineering

### Week 1: Runtime Fundamentals for AI Workloads
**Lecture Topics:**
- What a model runtime does: tokenization, batching, memory management, decoding
- Why KAZCADE_RUNTIME uses a custom runtime instead of raw PyTorch
- Continuous batching vs. static batching: throughput implications
- Memory layout: KV cache, weight tensors, activation buffers

**Lab Exercise:**
```python
from kazcade import Runtime, ModelConfig
config = ModelConfig(
    model_path="./models/mistral-7b-q4",
    max_batch_size=8,
    max_sequence_length=2048,
    kv_cache_dtype="float16"
)
rt = Runtime(config)
rt.load()
outputs = rt.generate(["Hello, world!", "Explain quantum entanglement"],
                       max_new_tokens=100)
for o in outputs:
    print(o.text)
```

### Week 2: Performance Tuning and Quantization
**Lecture Topics:**
- INT4/INT8 quantization: accuracy vs. speed tradeoffs
- Flash Attention 2 integration in KAZCADE
- Speculative decoding: draft model + verifier model
- Profiling: identifying bottlenecks in the forward pass

**Lab Exercise:**
```python
from kazcade import Runtime, ModelConfig, QuantConfig
quant = QuantConfig(method="awq", bits=4, group_size=128)
config = ModelConfig(model_path="./models/llama3-8b", quant=quant)
rt = Runtime(config)
rt.load()
import time
prompts = ["Summarize the Anticloud architecture"] * 16
start = time.time()
outputs = rt.generate(prompts, max_new_tokens=200)
elapsed = time.time() - start
tokens = sum(len(o.token_ids) for o in outputs)
print(f"Throughput: {tokens/elapsed:.1f} tokens/sec")
```

### Week 3: KAZCADE_RUNTIME in the Anticloud Stack
**Lecture Topics:**
- KAZCADE as the runtime layer beneath PAX_INFERENCE_CORE
- Runtime-level AIOSS logging for compliance
- Connecting KAZCADE to KANTOR_K5 for distributed serving
- Benchmarking on Kaggle T4 vs. local Quadro GPUs

**Lab Exercise:**
```python
from kazcade import Runtime
from pax_client import PAXInferenceCore
rt = Runtime.from_pax_config("pax_inference.yaml")
pax = PAXInferenceCore(runtime=rt)
pax.serve(host="0.0.0.0", port=8080)
```

## Exam Questions
1. Explain continuous batching. Why does it improve GPU utilization compared to static batching for LLM serving?
2. What is the quality-speed tradeoff when using AWQ INT4 quantization? At what use case threshold would you prefer INT8 over INT4?
3. How does speculative decoding reduce latency without reducing output quality? What property must the draft model have?
