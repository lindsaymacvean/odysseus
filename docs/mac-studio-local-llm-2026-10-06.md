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
| qwen3:32b | 20 GB | Ollama | Dense 32B. Good for tool calling, ~10 tok/s |
| qwen3-coder:30b | 18 GB | Ollama | MoE 30B (~3B active). Added 2026-10-06. Fastest model on the box |
| qwen3:8b | 5 GB | Ollama | Faster, less capable |
| qwen2.5vl:7b | 6 GB | Ollama | Vision (image understanding), not image generation |
| Llama-3.3-70B-Instruct-4bit | 37 GB | HuggingFace | Via mlx-flash SSD streaming |

No image-generation model is installed (no mflux, ComfyUI, Draw Things).

## Capacity benchmark — 2026-10-09

Ollama 0.32.14, all Q4_K_M. Server config: `OLLAMA_NUM_PARALLEL=1`, `OLLAMA_FLASH_ATTENTION=false`, KV cache fp16, keep-alive 5m. GPU-usable memory reported by Ollama: **51.8 GiB** (`iogpu.wired_limit_mb` = 0, i.e. macOS default). Prompts were synthetic business-text filler, 200-token output cap, `think: false` for qwen3.

### Max context and memory (resident, model + KV cache)

| Model | Max context (Ollama tag) | Memory at max context |
|-------|------|------|
| qwen3:8b | 40,960 | 11.8 GB |
| qwen2.5vl:7b | 128,000 | 14.9 GB (8.8 GB at 32K) |
| qwen3-coder:30b | 262,144 | 45.6 GB at 256K, 32.2 GB at 128K, 25.5 GB at 64K |
| qwen3:32b | 40,960 | 31.9 GB |

### Latency running alone (time to first token / generation speed)

| Prompt tokens | qwen3:8b | qwen2.5vl:7b | qwen3-coder:30b | qwen3:32b |
|------|------|------|------|------|
| ~300 | 0.6 s / 45 tok/s | 0.5 s / 50 | 0.4 s / 72 | 3 s / 11 |
| ~2.3K | 5 s / 43 | 6 s / 48 | 3 s / 65 | 29 s / 11 |
| ~9.3K | 26 s / 36 | 25 s / 43 | 16 s / 48 | 136 s / 9 |
| ~18.6K | 62 s / 31 | — | — | 311 s / 8 |
| ~37.8K | 171 s / 24 | 148 s / 30 | 153 s / 25 | 796 s / 6 |
| ~76.6K | — | — | 554 s / 15 | — |

- Image input (1024px JPEG, ~1.1K image tokens) on qwen2.5vl: 6.6 s to first token.
- Cold load adds ~3–4 s (7B/8B) or ~9–10 s (30B/32B).
- Prompt processing slows with depth (quadratic attention, flash attention off): coder drops from ~840 tok/s at 2K to ~140 tok/s at 77K.
- Prompts longer than `num_ctx` are silently truncated by Ollama (to roughly half the window), not rejected — the app must count tokens itself.

### Concurrency

- 8B + 32B + VL (32K) all fit at once: 11.8 + 31.4 + 8.8 = **52 GB**, right at the GPU limit. Loading coder (64K) on top **evicted 8B and 32B**.
- Running all three simultaneously, they share one GPU: **the small models collapse to ~2–4 tok/s** and their time to first token rises 4–5×, while the 32B barely slows (8.6–9.3 tok/s). Concurrent ≠ parallel throughput on one M1 Max.
- Coder at 256K context occupies ~46 GB alone — effectively nothing else can be loaded.

### Implications

- For the buddy, **qwen3-coder:30b (MoE) beats qwen3:32b** on every latency metric and has 6× the context; the dense 32B is only viable for short prompts or batch work.
- Interactive budget: keep prompts under ~8K tokens for sub-30 s replies. 32K+ contexts mean 2.5–13 min waits — retrieval/selection matters more than raw window size.
- Untested levers: `OLLAMA_FLASH_ATTENTION=1` + `OLLAMA_KV_CACHE_TYPE=q8_0` should roughly halve KV memory and speed long prompts; raising `iogpu.wired_limit_mb` gives more GPU headroom.

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

*Last updated: 2026-10-09*
