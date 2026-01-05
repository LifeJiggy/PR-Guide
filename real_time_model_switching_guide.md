# Real-time Model Switching User Guide

## Overview

The Real-time Model Switching feature enables seamless hot-swapping between different model variants without service interruption. This guide covers installation, configuration, usage, and best practices.

## Features

- **Zero Downtime**: Switch models while serving requests
- **Multiple Strategies**: Immediate, gradual rollout, A/B testing, canary deployments
- **Dynamic Selection**: Runtime model switching based on input characteristics
- **Emergency Rollback**: Instant rollback to previous versions
- **Health Monitoring**: Automatic performance tracking and alerting
- **REST API**: Full programmatic control over switching operations

## Installation

### Prerequisites

- vLLM-Omni installed
- Python 3.8+
- FastAPI (for API endpoints)

### Configuration

Add to your vLLM configuration:

```python
from vllm_omni.model_executor.models.config import ModelSwitchingConfig

# Configure model switching
switching_config = ModelSwitchingConfig(
    enable_dynamic_registry=True,
    max_cached_models=5,
    enable_health_monitoring=True,
    enable_management_api=True,
    api_port=8001
)
```

### Environment Variables

```bash
# Enable model switching
export VLLM_MODEL_SWITCHING_ENABLE_REGISTRY=true
export VLLM_MODEL_SWITCHING_MAX_CACHED_MODELS=5
export VLLM_MODEL_SWITCHING_ENABLE_API=true
export VLLM_MODEL_SWITCHING_API_PORT=8001

# Health monitoring
export VLLM_MODEL_SWITCHING_ENABLE_MONITORING=true
export VLLM_MODEL_SWITCHING_ERROR_RATE_WARNING=0.05
export VLLM_MODEL_SWITCHING_LATENCY_WARNING=5000
```

## Quick Start

### 1. Initialize Components

```python
from vllm_omni.model_executor.models import (
    DynamicModelRegistry, ModelSwitcher, HealthMonitor,
    TransitionManager, ModelCache
)
from vllm_omni.model_executor.models.config import get_model_switching_config

# Get configuration
config = get_model_switching_config()

# Initialize components
registry = DynamicModelRegistry(base_registry, **config.__dict__)
cache = ModelCache(max_cache_size=config.max_cache_size)
transition_manager = TransitionManager()
health_monitor = HealthMonitor()

# Create switcher
switcher = ModelSwitcher(registry, cache, transition_manager)

# Start background tasks
await registry.start_cleanup_task()
await cache.start_background_tasks()
await health_monitor.start_monitoring()
```

### 2. Register Models

```python
from vllm_omni.config.model import OmniModelConfig

# Register model versions
config_v1 = OmniModelConfig(
    model="Qwen/Qwen2.5-Omni-7B",
    model_arch="Qwen2_5OmniForConditionalGeneration",
    model_stage="thinker"
)

config_v2 = OmniModelConfig(
    model="Qwen/Qwen2.5-Omni-7B-v2",
    model_arch="Qwen2_5OmniForConditionalGeneration",
    model_stage="thinker"
)

# Register versions
model_id = registry.register_model(config_v1, "v1.0", {"author": "team"})
registry.register_model(config_v2, "v2.0", {"author": "team", "improvements": "latency"})
```

### 3. Switch Models

```python
# Immediate switch
result = await switcher.switch_model(
    model_id="Qwen2_5OmniForConditionalGeneration_thinker",
    target_version="v2.0",
    strategy_type=SwitchingStrategyType.IMMEDIATE
)

print(f"Switch {'successful' if result.success else 'failed'}: {result.operation_id}")
```

## Switching Strategies

### Immediate Switching

Instantly switches all traffic to the new model version.

```python
result = await switcher.switch_model(
    model_id="my_model",
    target_version="v2.0",
    strategy_type=SwitchingStrategyType.IMMEDIATE
)
```

**Use Case**: Emergency fixes, small non-breaking changes

### Gradual Rollout

Gradually increases traffic to the new version over time.

```python
result = await switcher.switch_model(
    model_id="my_model",
    target_version="v2.0",
    strategy_type=SwitchingStrategyType.GRADUAL,
    strategy_config={
        "duration_minutes": 30,
        "steps": 10
    }
)
```

**Use Case**: Major updates, performance monitoring

### A/B Testing

Routes traffic between versions for experimentation.

```python
result = await switcher.switch_model(
    model_id="my_model",
    target_version="v2.0",
    strategy_type=SwitchingStrategyType.AB_TEST,
    strategy_config={
        "traffic_percentage": 20,
        "test_duration_hours": 48
    }
)
```

**Use Case**: Feature testing, performance comparison

### Canary Deployment

Starts with small traffic percentage, increases based on success.

```python
result = await switcher.switch_model(
    model_id="my_model",
    target_version="v2.0",
    strategy_type=SwitchingStrategyType.CANARY,
    strategy_config={
        "initial_percentage": 5,
        "step_percentage": 15,
        "evaluation_period_minutes": 30
    }
)
```

**Use Case**: High-risk changes, automated validation

## REST API Usage

### Start the API Server

```python
from vllm_omni.model_executor.models.api import create_model_switching_api

# Create API instance
api = create_model_switching_api(registry, switcher, health_monitor)

# Start server
await api.start_server()
```

### API Endpoints

#### Register a Model

```bash
curl -X POST http://localhost:8001/models \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Qwen/Qwen2.5-Omni-7B",
    "model_arch": "Qwen2_5OmniForConditionalGeneration",
    "model_stage": "thinker",
    "version": "v1.0",
    "metadata": {"author": "team"}
  }'
```

#### Switch Models

```bash
curl -X POST http://localhost:8001/switch \
  -H "Content-Type: application/json" \
  -d '{
    "model_id": "Qwen2_5OmniForConditionalGeneration_thinker",
    "target_version": "v2.0",
    "strategy": "gradual",
    "strategy_config": {
      "duration_minutes": 15,
      "steps": 5
    }
  }'
```

#### Check Switch Status

```bash
curl http://localhost:8001/switch/operations/{operation_id}
```

#### Monitor Health

```bash
curl http://localhost:8001/health/models
```

#### View Active Alerts

```bash
curl http://localhost:8001/alerts
```

## Monitoring and Alerts

### Health Metrics

The system automatically tracks:

- Request count and error rates
- Response latency (P50, P95, P99)
- Memory and CPU usage
- Custom performance metrics

### Alert Types

- **Error Rate**: High error rates trigger warnings/critical alerts
- **Latency**: Slow responses trigger performance alerts
- **Resource Usage**: Memory/CPU threshold violations

### Custom Alert Handlers

```python
def custom_alert_handler(alert):
    # Send to Slack, PagerDuty, etc.
    print(f"Alert: {alert.title} - {alert.message}")

alert_system = AlertSystem(alert_handlers=[custom_alert_handler])
health_monitor = HealthMonitor(alert_system=alert_system)
```

## Integration with vLLM Engine

### Request Processing Integration

```python
class SwitchingEngine(vllm.LLM):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

        # Initialize switching components
        self.switcher = ModelSwitcher(...)
        self.transition_manager = TransitionManager()

    def _process_request(self, request):
        # Check for active transitions
        model_id = self._get_model_id_for_request(request)
        assigned_model = self.transition_manager.handle_request_routing(model_id, request.id)

        if assigned_model:
            # Route to specific model instance
            return self._route_to_model(assigned_model, request)
        else:
            # Use normal routing
            return super()._process_request(request)

    def _record_request_metrics(self, request, response, latency_ms, error=False):
        # Record metrics for health monitoring
        model_key = f"{request.model_id}_{request.model_version}"
        self.health_monitor.record_request(model_key, latency_ms, error)
```

### Configuration Integration

```python
# In your vLLM config
from vllm_omni.model_executor.models.config import ModelSwitchingConfig

engine_config = vllm.EngineConfig(
    # ... other config
    model_switching=ModelSwitchingConfig.from_env()
)
```

## Best Practices

### 1. Pre-load Models

```python
# Pre-load models into cache
cache.preload_model("my_model_v2.0", priority=10)
```

### 2. Monitor Health Before Switching

```python
# Check model health before switching
validation = switcher.validate_switch_request(model_id, target_version)
if not validation["valid"]:
    print("Validation failed:", validation["errors"])
```

### 3. Use Appropriate Strategies

- **Immediate**: For patches, small fixes
- **Gradual**: For major updates, monitor impact
- **A/B Test**: For feature validation
- **Canary**: For high-risk changes

### 4. Set Up Monitoring

```python
# Configure alerts
health_monitor.update_thresholds({
    "error_rate_warning": 0.03,
    "latency_p95_warning": 3000
})
```

### 5. Plan Rollbacks

```python
# Always have rollback versions ready
version_manager.create_version(model_id, config, {"rollback": True})
```

## Troubleshooting

### Common Issues

#### Switch Operation Fails

```python
# Check operation status
status = switcher.get_switch_status(operation_id)
print(f"Status: {status['status']}, Progress: {status['progress']}")

# Check validation
validation = switcher.validate_switch_request(model_id, target_version)
print("Validation:", validation)
```

#### High Latency During Transitions

- Reduce transition duration
- Use smaller step sizes in gradual rollouts
- Pre-load target models

#### Memory Issues

- Reduce max_cached_models
- Increase cache eviction intervals
- Monitor memory usage trends

### Logs and Debugging

```python
import logging
logging.getLogger("vllm_omni.model_executor.models").setLevel(logging.DEBUG)
```

## Performance Considerations

### Memory Management

- Models are cached with LRU eviction
- Memory limits prevent OOM issues
- Automatic cleanup of unused models

### Switching Performance

- Immediate switches: <5 seconds
- Gradual rollouts: Configurable duration
- Zero impact on in-flight requests

### Monitoring Overhead

- Minimal performance impact (<1%)
- Configurable monitoring intervals
- Efficient metric storage

## Security Considerations

### API Security

```python
# Enable authentication
config = ModelSwitchingConfig(
    enable_authentication=True,
    api_key="your-secure-api-key"
)
```

### Access Control

- Limit API access to authorized users
- Use HTTPS in production
- Audit all switching operations

## Examples

### Complete Switching Workflow

```python
import asyncio
from vllm_omni.model_executor.models import *

async def switching_workflow():
    # Initialize
    registry = DynamicModelRegistry(...)
    switcher = ModelSwitcher(...)

    # Register models
    registry.register_model(config_v1, "v1.0")
    registry.register_model(config_v2, "v2.0")

    # Gradual rollout
    result = await switcher.switch_model(
        "my_model", "v2.0",
        SwitchingStrategyType.GRADUAL,
        {"duration_minutes": 10}
    )

    # Monitor progress
    while True:
        status = switcher.get_switch_status(result.operation_id)
        print(f"Progress: {status['progress']:.1%}")
        if status['status'] in ['completed', 'failed']:
            break
        await asyncio.sleep(30)

    print(f"Switch completed: {result.success}")
```

### Health Monitoring Setup

```python
def alert_to_slack(alert):
    # Send to Slack webhook
    requests.post(SLACK_WEBHOOK, json={
        "text": f"🚨 Model Alert: {alert.title}\n{alert.message}"
    })

alert_system = AlertSystem([alert_to_slack])
health_monitor = HealthMonitor(alert_system)

# Monitor specific metrics
health_monitor.record_request("model_v1.0", latency_ms=150, error=False)
```

This guide provides comprehensive coverage of the Real-time Model Switching feature. For additional support, check the API documentation or open an issue in the repository.</content>
