# OpenClaw Model Manager

Multi-model router for OpenClaw with 5-tier structure and task decomposition.

## Overview

A sophisticated model selection system that:
- Uses **5 tiers** of model complexity
- Supports **4 providers** per tier (MiniMax, Google, OpenAI, Anthropic/xAI/Meta)
- Covers **3 categories**: General/Text, Vision, Image Generation
- Implements **multi-model routing** for complex workflows

## Architecture

### Tier Structure

| Tier | Complexity | Use Case |
|------|------------|----------|
| 1 | Ultra-Light | Fast web browsing, simple extraction |
| 2 | Light | Quick summaries, basic tasks |
| 3 | Standard | Balanced general-purpose work |
| 4 | Capable | Complex reasoning, analysis |
| 5 | Heavy | Critical tasks, deep reasoning |

### Categories

| Category | Description | Default Model |
|----------|-------------|---------------|
| General/Text | LLM tasks | `codex-mini` (Tier 2) |
| Vision | Image analysis | `gemini-2.0-pro-vision` (Tier 3) |
| Image Generation | Create images | `dalle-3` (Tier 3) |

### Providers (per tier)

- **MiniMax** / **Stability AI** (budget options)
- **Google** (Gemini family)
- **OpenAI** (GPT family)
- **Anthropic** (Claude family) / **xAI** (Grok) / **Meta** (Llama)

## Selection Heuristic

1. **Low confidence** → bump thinking level first
2. **Still struggling** → escalate to next tier
3. **Complex multi-step** → start at Tier 3+ with thinking enabled

## Multi-Model Router

For complex tasks, decompose into sub-tasks and assign optimal models per sub-task.

### Supported Task Types

| Task Type | Tier | Thinking | Example Models |
|-----------|------|----------|----------------|
| Web Browsing | 1-2 | OFF | `gemini-2.0-flash`, `gpt-4.1-nano` |
| Data Extraction | 2 | OFF | `gpt-4.1-nano`, `claude-3-haiku` |
| Analysis/Reasoning | 3-4 | **ON** | `claude-3-7-sonnet`, `grok-3` |
| Code Generation | 3-4 | ON | `gpt-4o`, `claude-3-5-sonnet` |
| Text Summarization | 2 | OFF | `gpt-4.1-nano`, `claude-3-haiku` |
| Creative Writing | 3 | OFF-ON | `gpt-4o`, `claude-3-7-sonnet` |
| Planning/Strategy | 4-5 | **ON** | `claude-4-opus`, `grok-3-think` |
| Image Generation | 3 | OFF | `dalle-3`, `imagen-3-pro` |
| Image Analysis | 3-4 | ON | `claude-3-7-sonnet-vision` |

## Example Workflows

### 1. Trading Research → Signal (IDX Stocks)

```
Data Fetch (T1) → Technical Analysis (T3) → Patterns (T4) → Signal (T4) → Summary (T2)
```

| Sub-Task | Type | Tier | Model | Thinking |
|----------|------|------|-------|----------|
| Fetch stock data | Data | 1 | `gemini-2.0-flash` | OFF |
| Calculate indicators | Code | 3 | `gpt-4o` | ON |
| Identify patterns | Analysis | 4 | `claude-3-5-sonnet` | ON |
| Generate signal | Analysis | 4 | `grok-3` | ON |
| Summarize | Text | 2 | `claude-3-haiku` | OFF |

### 2. TikTok Ad Creation (E-commerce)

```
Trends (T1) → Analysis (T3) → Script (T3) → Image (T3) → Compile (T2)
```

### 3. Robot Debugging (ESP32/Koda)

```
Logs (T1) → Patterns (T4) → Root Cause (T4) → Fix (T4) → Verify (T3) → Doc (T2)
```

### 4. Blog Post (Content Marketing)

```
Scraping (T1) → Extract (T2) → Analysis (T3) → Outline (T3) → Write (T3) → Image (T3) → SEO (T3)
```

## Files

| File | Purpose |
|------|---------|
| `ENABLED_MODELS.md` | Full model tier list and router logic |
| `SOUL.md` | Agent directives (OpenClaw-specific) |

## Usage

1. Match task complexity to a tier
2. For multi-step tasks, decompose and assign per sub-task
3. Apply thinking mode for reasoning-heavy steps
4. Fallback: retry → escalate tier → try alternative provider

## License

MIT
