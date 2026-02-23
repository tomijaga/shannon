# Using Shannon with Ollama Models

Shannon supports Ollama models through Claude Code's native Anthropic API compatibility (requires Ollama v0.14.0+). This lets you run Shannon with local open-source models or cloud models from ollama.com — no code changes needed.

## Requirements

- [Ollama installed and running](https://ollama.com/download) on your machine
- Ollama v0.14.0+ (for Anthropic API compatibility)

## Quick Start

### Option 1: Local Models (Free, Private)

```bash
# 1. Pull a model
ollama pull qwen3-coder

# 2. Configure .env
ANTHROPIC_AUTH_TOKEN=ollama
ANTHROPIC_BASE_URL=http://localhost:11434

# 3. Run Shannon
./shannon start URL=http://target.com REPO=my-repo
```

### Option 2: Cloud Models (ollama.com account required)

Cloud models use the `:cloud` suffix. The Ollama daemon routes them to ollama.com — you still point `ANTHROPIC_BASE_URL` at your **local Ollama server**, not at ollama.com directly.

```bash
# 1. Log in to ollama.com via your local Ollama daemon
ollama login

# 2. Configure .env
ANTHROPIC_AUTH_TOKEN=ollama
ANTHROPIC_BASE_URL=http://localhost:11434

# 3. Run Shannon
./shannon start URL=http://target.com REPO=my-repo
```

## Configuration

Add to your `.env` file (or copy `.env.ollama.example`):

```bash
ANTHROPIC_AUTH_TOKEN=ollama
ANTHROPIC_BASE_URL=http://localhost:11434   # always your local Ollama server
```

| Variable | Description |
|----------|-------------|
| `ANTHROPIC_AUTH_TOKEN=ollama` | Signals Shannon to use Ollama |
| `ANTHROPIC_BASE_URL` | URL of your Ollama server (default: `http://localhost:11434`) |

## Recommended Models

### Cloud Models (`:cloud` suffix — routed by Ollama to ollama.com)

| Model | Best For |
|-------|----------|
| `glm-5:cloud` | Default, high performance |
| `minimax-m2.1:cloud` | Fast tasks |
| `qwen3-coder:480b` | Code-heavy analysis |

### Local Models (run on your machine — pull first)

| Model | Size | Best For |
|-------|------|----------|
| `qwen3-coder` | 30B | Coding, vuln analysis |
| `gpt-oss:20b` | 20B | General pentesting |
| `gpt-oss:120b` | 120B | Complex analysis (needs GPU) |

```bash
# Pull a local model before use
ollama pull qwen3-coder
```

## Context Size

⚠️ Claude Code's default system prompt is ~16.5K tokens. Smaller models may struggle.

```bash
# Lower max output tokens for smaller models
CLAUDE_CODE_MAX_OUTPUT_TOKENS=32000
```

## Troubleshooting

### "Failed to connect to Ollama"

```bash
# Ensure Ollama is running
ollama serve

# Test the connection
curl http://localhost:11434/api/tags
```

### "unauthorized" on cloud models

```bash
# Log in to ollama.com via the Ollama daemon
ollama login
```

### Model gives nonsensical output

The model is likely too small for Claude Code's system prompt. Use a model >70B parameters, or switch to a cloud model (`glm-5:cloud`).

### Out of memory

```bash
# Use a smaller quantized variant
ollama pull qwen3-coder:q4_0

# Or switch to a cloud model
OLLAMA_MODEL=glm-5:cloud
```

## References

- [Ollama Anthropic API Compatibility](https://ollama.com/blog/anthropic-api)
- [Ollama Model Library](https://ollama.com/library)
- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
