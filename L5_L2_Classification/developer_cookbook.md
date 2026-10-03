# Developer Cookbook — KAZCADE_RUNTIME
**Stack:** Python 3.11, asyncio, multiprocessing, psutil

## Register and start modules
```python
from kazcade_runtime import KazcadeRuntime, ModuleConfig
rt = KazcadeRuntime(aioss_chain="./runtime.aioss")
rt.register(ModuleConfig(name="PAX_INFERENCE", path="./pax_harness.py", gpu=True))
rt.register(ModuleConfig(name="KAMELOT_SEARCH", path="./kamelot_search.py", gpu=False))
rt.start_all()
```

## Dispatch request to module
```python
response = await rt.dispatch("PAX_INFERENCE", {"query": "Analyze biosignal", "data": eeg_bytes})
```

## Health check
```python
status = rt.health_check_all()
for mod, health in status.items():
    print(f"{mod}: {'OK' if health.alive else 'DEAD'} — {health.memory_mb}MB")
```

## Graceful shutdown
```python
rt.shutdown(flush_aioss=True, timeout=30)
```

## Performance
`rt.preload_models()` at startup avoids cold-start. Per-module memory limits:
`ModuleConfig(memory_limit_mb=8192)`. `max_concurrent_requests` per GPU memory.

## Integration
Execution environment for all TIER_2 PAX_* modules. Integrates with AIOSS_FORMAT,
KAMELOT_SEARCH, INTE11ECT_APP.
