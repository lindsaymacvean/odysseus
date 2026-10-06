# Mac Studio Local LLM Testing — 2026-10-06

Testing SSD weight streaming and agentic tools on the Mac Studio (M1 Max, 64GB).

## Hardware

| Spec | Value |
|------|-------|
| Model | Mac Studio (M1 Max) |
| Memory | 64 GB unified |
| SSD | ~2.9 GB/s write speed |
| Tailscale | `mini-01-591.tailb6b3a8.ts.net` |

## Goal

Replicate Brian Roemmele's demo of running 125B models locally via SSD weight streaming (Strata). Test whether 70B models now work on 64GB when they previously failed.

## Tools Tested

### MLX-Flash (matt-k-wong fork) — Recommended

**Repo:** https://github.com/matt-k-wong/mlx-flash

**Status:** Working. This is the correct version with SSD weight streaming.

**Installation:**
```bash
# Create Python 3.12 venv
/opt/homebrew/opt/python@3.12/bin/python3.12 -m venv ~/mlx-env
source ~/mlx-env/bin/activate

# Install (NOT from PyPI — that's a different package)
pip install git+https://github.com/matt-k-wong/mlx-flash.git
```

**Usage:**
```bash
source ~/mlx-env/bin/activate
mlx-flash --model mlx-community/Llama-3.3-70B-Instruct-4bit \
  --ram 20 \
  --kv-quant 8 \
  --prompt "Your prompt here" \
  --max-tokens 200
```

**Results:**
| RAM Budget | Speed | Notes |
|------------|-------|-------|
| 30 GB | ~2.5 tok/s | Model barely fits, memory pressure warning |
| 20 GB | ~2.5 tok/s | More headroom, same speed |
| 8 GB | ~0.3 tok/s | Too aggressive, sequential IO |

**Verdict:** 70B runs but slowly (~2.5 tok/s). Usable for batch tasks, not interactive chat.

### MLX-Flash (szibis/mlx-flash) — Not Recommended

**Repo:** https://github.com/szibis/mlx-flash (also on PyPI as `mlx-flash`)

**Status:** Different project. This is an inference server, not the SSD streaming tool.

**Notes:** Installs via `pip install mlx-flash` but does NOT have SSD weight streaming. Loads entire model into RAM.

### Strata (Apple Silicon fork)

**Repo:** https://github.com/Ikaikaalika/strata

**Status:** Research project, not production-ready.

**Notes:** Requires manual integration, demos use small models (1.3B). The SSD weight streaming is in development but not usable for 70B inference yet.

### OpenClaw

**Repo:** https://openclaw.ai/

**Status:** Installed, partially working.

**Installation:**
```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**Configuration:**
```bash
export OLLAMA_API_KEY='local'  # Required even for local Ollama
```

**Results:**
| Mode | Status | Notes |
|------|--------|-------|
| Basic chat | ✅ Works | `openclaw agent exec "prompt" --model ollama/qwen3:32b` |
| Code mode | ❌ Fails | Tool schema violations, empty responses |

**Verdict:** Agentic code mode doesn't work with local Ollama models (as of v2026.9.8).

## Tool Calling via Ollama

Direct Ollama API supports tool calling with Qwen3:

```bash
curl -s http://localhost:11434/api/chat -d '{
  "model": "qwen3:32b",
  "messages": [{"role": "user", "content": "What is the weather in London?"}],
  "tools": [{
    "type": "function",
    "function": {
      "name": "get_weather",
      "description": "Get weather for a city",
      "parameters": {
        "type": "object",
        "properties": {
          "city": {"type": "string", "description": "City name"}
        },
        "required": ["city"]
      }
    }
  }],
  "stream": false
}'
```

**Result:** Correctly returns `{"tool_calls": [{"function": {"name": "get_weather", "arguments": {"city": "London"}}}]}`

## Models on Mac Studio

| Model | Size | Source | Notes |
|-------|------|--------|-------|
| qwen3:32b | 20 GB | Ollama | Good for tool calling, ~10 tok/s |
| qwen3:8b | 5 GB | Ollama | Faster, less capable |
| qwen2.5vl:7b | 6 GB | Ollama | Vision model |
| Llama-3.3-70B-Instruct-4bit | 37 GB | HuggingFace | Via mlx-flash SSD streaming |

## What Changed (vs Previous Attempts)

Previously, 70B models failed because:
1. MLX loaded entire model into unified memory
2. macOS swapped uncontrollably to disk
3. Result: 2-5 tok/s with system freezing

Now with MLX-Flash (matt-k-wong):
1. Controlled SSD streaming of cold layers
2. Hot layers cached in RAM
3. Result: ~2.5 tok/s, stable, no system freeze

**Improvement:** From "unusable" to "slow but stable."

## Recommendations

1. **For 70B inference:** Use MLX-Flash with `--ram 20` for best balance
2. **For tool calling:** Use Ollama directly with Qwen3:32b
3. **For agentic coding:** Wait for OpenClaw fixes or use Claude Code remotely
4. **Monitor:** These tools are rapidly evolving; re-test monthly

## Files Installed

| Path | Purpose |
|------|---------|
| `~/mlx-env/` | Python 3.12 venv with mlx-flash |
| `~/strata/` | Strata clone (not production-ready) |
| `~/.cache/huggingface/` | Model weights (~67 GB total) |
| `/opt/homebrew/bin/openclaw` | OpenClaw CLI |

## Next Steps

- [ ] Re-test when mlx-flash or Strata release updates
- [ ] Try MoE models (Mixtral) which may benefit more from expert streaming
- [ ] Test oMLX for KV cache persistence in long coding sessions
- [ ] Report OpenClaw code-mode issues to their GitHub

---

*Last updated: 2026-10-06*
