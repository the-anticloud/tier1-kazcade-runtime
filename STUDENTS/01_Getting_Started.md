# KAZCADE_RUNTIME — Student Getting Started

## What You'll Build
A high-performance model serving runtime that loads quantized LLMs and generates text with continuous batching and memory-efficient KV caching.

## Prerequisites
- Python 3.10+
- NVIDIA GPU (or CPU-only mode for testing)
- CUDA toolkit (optional, for GPU mode)

## Install
```bash
pip install kazcade-runtime
```

## First Working Example
```python
from kazcade import Runtime, ModelConfig

config = ModelConfig(
    model_path="./models/mistral-7b-instruct-q4",  # GGUF or AWQ format
    max_batch_size=4,
    max_sequence_length=2048
)

rt = Runtime(config)
rt.load()
print("Model loaded!")

outputs = rt.generate(
    ["What is continuous batching?", "Explain KV cache memory layout"],
    max_new_tokens=150
)
for prompt, output in zip(["Q1", "Q2"], outputs):
    print(f"\n{prompt}: {output.text}")
```

## Download a Quantized Model
```bash
# Download a 4-bit quantized model (fits in 5 GB VRAM)
pip install huggingface_hub
python -c "from huggingface_hub import snapshot_download; snapshot_download('TheBloke/Mistral-7B-Instruct-v0.2-AWQ', local_dir='./models/mistral-7b-awq')"
```

## On Kaggle (loiskleinner account, T4 GPU)
```python
# Kaggle T4 has 15 GB VRAM — can run 13B models
!pip install kazcade-runtime
from kazcade import Runtime, ModelConfig, QuantConfig
quant = QuantConfig(method="awq", bits=4)
config = ModelConfig(model_path="./models/llama3-8b-awq", quant=quant)
rt = Runtime(config); rt.load()
print(rt.generate(["Hello!"], max_new_tokens=50)[0].text)
```

## What's Next
- Try `max_batch_size=8` and measure throughput improvement
- Compare INT4 vs INT8 quantization quality on the same prompt
- Hook into PAX_INFERENCE_CORE to serve via REST API
