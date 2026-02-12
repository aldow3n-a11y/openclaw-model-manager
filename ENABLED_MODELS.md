# Modeliers (5-T Tier × 4-Provider Structure)

## Selection Heuristic
1. **Low confidence** → bump thinking level first
2. **Still struggling** → escalate to next tier
3. **Complex multi-step** → start at Tier 3+ with thinking

---

# General / Text Models

### Tier 1: Ultra-Light (Fast / Cheapest)
1. `minimax-m2.1-lightning` — MiniMax (2025) 💰 cheapest
2. `gemini-2.0-flash` — Google (2025)
3. `gpt-4.1-mini` — OpenAI (2025)
4. `llama-4-scout` — Meta (2025)

### Tier 2: Light
1. `gpt-4.1-nano` — OpenAI (2025)
2. `gemini-2.0-flash-lite` — Google (2025)
3. `minimax-m2.1` — MiniMax (2025)
4. `claude-3-haiku` — Anthropic (2025)

### Tier 3: Standard (Balanced)
1. `gpt-4o` — OpenAI (2024-11) *(latest 2025-capable)*
2. `claude-3-7-sonnet` — Anthropic (2025)
3. `gemini-2.0-pro` — Google (2025)
4. `grok-2.5` — xAI (2025)

### Tier 4: Capable (Reasoning ON)
1. `grok-3` — xAI (2025)
2. `claude-3-5-sonnet` — Anthropic (2025)
3. `gpt-4.5-preview` — OpenAI (2025)
4. `gemini-2.0-ultra` — Google (2025)

### Tier 5: Heavy (Complex / Critical)
1. `claude-4-opus` — Anthropic (2025)
2. `gpt-4.5` — OpenAI (2025)
3. `grok-3-think` — xAI (2025)
4. `gemini-2.0-pro-001` — Google (2025, extended context)

---

# Image Processing Models

### Tier 1: Ultra-Light Vision (Fast / Cheapest)
1. `minimax-vision-m2.1-lightning` — MiniMax (2025) 💰 cheapest
2. `gemini-2.0-flash-vision` — Google (2025)
3. `gpt-4.1-mini-vision` — OpenAI (2025)
4. `llama-4-scout-vision` — Meta (2025)

### Tier 2: Light Vision
1. `gpt-4.1-nano-vision` — OpenAI (2025)
2. `gemini-2.0-flash-lite-vision` — Google (2025)
3. `minimax-vision-m2.1` — MiniMax (2025)
4. `claude-3-haiku-vision` — Anthropic (2025)

### Tier 3: Standard Vision (Balanced)
1. `gpt-4o-vision` — OpenAI (2024-11) *(latest 2025-capable)*
2. `claude-3-7-sonnet-vision` — Anthropic (2025)
3. `gemini-2.0-pro-vision` — Google (2025)
4. `grok-2.5-vision` — xAI (2025)

### Tier 4: Capable Vision (Reasoning ON)
1. `grok-3-vision` — xAI (2025)
2. `claude-3-5-sonnet-vision` — Anthropic (2025)
3. `gpt-4.5-preview-vision` — OpenAI (2025)
4. `gemini-2.0-ultra-vision` — Google (2025)

### Tier 5: Heavy Vision (Complex / Critical)
1. `claude-4-opus-vision` — Anthropic (2025)
2. `gpt-4.5-vision` — OpenAI (2025)
3. `grok-3-think-vision` — xAI (2025)
4. `gemini-2.0-ultra-002` — Google (2025, max resolution)

---

## Defaults
| Category | Default Model | Tier |
|----------|---------------|------|
| General | openai/codex-mini-latest | Tier 2 (light) |
| Vision | google/gemini-2.0-pro-vision | Tier 3 (standard) |
| Image Generation | openai/dalle-3 | Tier 3 (standard) |

## Thinking Mode
- **Tier 4+**: ON by default
- **Tier 1-3**: OFF, enable on low confidence

---

# Image Generation Models

### Tier 1: Ultra-Light (Fast / Cheapest)
1. `minimax-image-m2.1-lightning` — MiniMax (2025) 💰 cheapest
2. `stable-diffusion-xl-lite` — Stability AI (2025)
3. `imagen-3-fast` — Google (2025)
4. `dalle-3-mini` — OpenAI (2025)

### Tier 2: Light
1. `flux-schnell` — Black Forest Labs (2025)
2. `imagen-3` — Google (2025)
3. `stable-diffusion-3-medium` — Stability AI (2025)
4. `dalle-3-mini-hq` — OpenAI (2025)

### Tier 3: Standard (Balanced)
1. `midjourney-v6.1` — Midjourney (2025)
2. `dalle-3` — OpenAI (2025)
3. `imagen-3-pro` — Google (2025)
4. `stable-diffusion-3-large` — Stability AI (2025)

### Tier 4: Capable (High Quality)
1. `flux-pro` — Black Forest Labs (2025)
2. `dalle-3-hd` — OpenAI (2025)
3. `imagen-3-ultra` — Google (2025)
4. `stable-diffusion-xl-ultra` — Stability AI (2025)

### Tier 5: Heavy (Photorealistic / Complex)
1. `midjourney-v7-alpha` — Midjourney (2025)
2. `dalle-4` — OpenAI (2025)
3. `imagen-4` — Google (2025)
4. `stable-diffusion-4.0-ultra` — Stability AI (2025)

---

# Multi-Model Router (Task Decomposition)

## When to Use
For complex, multi-step tasks, decompose into sub-tasks and assign appropriate models per sub-task.

## Execution Flow

```
Task Input
    ↓
┌─────────────────────────────────────┐
│ Step 1: Classify Task Type          │
│ (Web, Analysis, Code, etc.)         │
└─────────────────────────────────────┘
    ↓
┌─────────────────────────────────────┐
│ Step 2: Decompose into Sub-Tasks    │
│ Identify distinct steps               │
└─────────────────────────────────────┘
    ↓
┌─────────────────────────────────────┐
│ Step 3: Assign Model per Sub-Task   │
│ Match complexity → Tier              │
└─────────────────────────────────────┘
    ↓
┌─────────────────────────────────────┐
│ Step 4: Execute Sequentially         │
│ Pass output as next input           │
└─────────────────────────────────────┘
    ↓
Final Output
```

---

## Task Type → Model Mapping

| Task Type | Complexity | Thinking? | Recommended Models (Tier) |
|-----------|------------|----------|--------------------------|
| **Web Browsing** | Low | OFF | Tier 1-2: `gemini-2.0-flash`, `gpt-4.1-nano` |
| **Data Extraction** | Low-Med | OFF | Tier 2: `gpt-4.1-nano`, `claude-3-haiku` |
| **Analysis/Reasoning** | Med-High | **ON** | Tier 3-4: `claude-3-7-sonnet`, `grok-3` |
| **Code Generation** | Med-High | ON | Tier 3-4: `gpt-4o`, `claude-3-5-sonnet` |
| **Text Summarization** | Low | OFF | Tier 2: `gpt-4.1-nano`, `claude-3-haiku` |
| **Creative Writing** | Med | OFF-ON | Tier 3: `gpt-4o`, `claude-3-7-sonnet` |
| **Planning/Strategy** | High | **ON** | Tier 4-5: `claude-4-opus`, `grok-3-think` |
| **Image Generation** | Med | OFF | Tier 3: `dalle-3`, `imagen-3-pro` |
| **Image Analysis** | Med | ON | Tier 3-4: `claude-3-7-sonnet-vision` |

---

## Example: "Browse TikTok viral content → Summary"

### Sub-Task Breakdown

| # | Sub-Task | Type | Tier | Model | Thinking |
|---|----------|------|------|-------|----------|
| 1 | Browse TikTok / Search | Web | 1 | `gemini-2.0-flash` | OFF |
| 2 | Extract viral patterns | Analysis | 3 | `claude-3-7-sonnet` | **ON** |
| 3 | Summarize findings | Text | 2 | `gpt-4.1-nano` | OFF |

### Execution

```
1. Browse (Tier 1) → Raw viral content data
   ↓
2. Analyze (Tier 3, Thinking ON) → Patterns, metrics, insights
   ↓
3. Summarize (Tier 2) → Final report
```

---

## Model Pool Reference

### Web Browsing (Tier 1-2)
| Model | Provider | Best For |
|-------|----------|----------|
| `gemini-2.0-flash` | Google | Fast web search |
| `gpt-4.1-nano` | OpenAI | Quick extraction |
| `minimax-m2.1` | MiniMax | Budget browsing |

### Analysis & Reasoning (Tier 3-4)
| Model | Provider | Best For |
|-------|----------|----------|
| `claude-3-7-sonnet` | Anthropic | Complex reasoning |
| `grok-3` | xAI | Deep analysis |
| `gemini-2.0-pro` | Google | Balanced reasoning |

### Generation (Tier 2-3)
| Model | Provider | Best For |
|-------|----------|----------|
| `gpt-4.1-nano` | OpenAI | Light summaries |
| `claude-3-haiku` | Anthropic | Quick writes |
| `gpt-4o` | OpenAI | Detailed output |

---

## Fallback Per Sub-Task
If a sub-task fails:
1. Retry with same model (transient error)
2. Escalate to next tier in same category
3. Try alternative provider in same tier

---

## Thinking Mode Guidelines

| Condition | Thinking |
|-----------|----------|
| Tier 4+ | **ON** by default |
| Analysis/Reasoning tasks | **ON** |
| Simple extraction/summary | OFF |
| Low confidence during execution | **ON** (bump up) |

---

## Example Workflows

### Example 1: "Research IDX stock, analyze with technical indicators, create trading signal"

**Context**: Swing trading research for BUMI stock

| # | Sub-Task | Type | Tier | Model | Thinking |
|---|----------|------|------|-------|----------|
| 1 | Fetch stock data (price, volume) | Data | 1 | `gemini-2.0-flash` | OFF |
| 2 | Calculate technical indicators | Code/Analysis | 3 | `gpt-4o` | **ON** |
| 3 | Identify chart patterns | Analysis | 4 | `claude-3-5-sonnet` | **ON** |
| 4 | Generate trading signal | Analysis | 4 | `grok-3` | **ON** |
| 5 | Summarize with entry/exit | Text | 2 | `claude-3-haiku` | OFF |

**Execution Flow**:
```
Data (Tier 1) → Technical Analysis (Tier 3) → Patterns (Tier 4) → Signal (Tier 4) → Summary (Tier 2)
```

---

### Example 2: "Create TikTok Ad for HOMU: Research trends → Script → Generate image → Compile"

**Context**: E-commerce marketing workflow

| # | Sub-Task | Type | Tier | Model | Thinking |
|---|----------|------|------|-------|----------|
| 1 | Research TikTok trends | Web | 1 | `gemini-2.0-flash` | OFF |
| 2 | Analyze competitor ads | Analysis | 3 | `claude-3-7-sonnet` | **ON** |
| 3 | Write ad script | Creative | 3 | `gpt-4o` | OFF |
| 4 | Generate product image | Image Gen | 3 | `dalle-3` | OFF |
| 5 | Compile final ad package | Assembly | 2 | `gpt-4.1-nano` | OFF |

**Execution Flow**:
```
Trends (Tier 1) → Analysis (Tier 3) → Script (Tier 3) → Image (Tier 3) → Compile (Tier 2)
```

---

### Example 3: "Debug Koda Robot: Analyze logs → Identify issue → Generate fix → Verify"

**Context**: ESP32 robot debugging workflow

| # | Sub-Task | Type | Tier | Model | Thinking |
|---|----------|------|------|-------|----------|
| 1 | Fetch system logs | Data | 1 | `minimax-m2.1` | OFF |
| 2 | Analyze error patterns | Analysis | 4 | `claude-3-5-sonnet` | **ON** |
| 3 | Identify root cause | Reasoning | 4 | `grok-3` | **ON** |
| 4 | Generate fix code | Code | 4 | `gpt-4o` | **ON** |
| 5 | Verify fix logic | Code Review | 3 | `claude-3-7-sonnet` | **ON** |
| 6 | Document changes | Text | 2 | `claude-3-haiku` | OFF |

**Execution Flow**:
```
Logs (Tier 1) → Patterns (Tier 4) → Root Cause (Tier 4) → Fix (Tier 4) → Verify (Tier 3) → Doc (Tier 2)
```

---

### Example 4: "Product Research → Competitor Analysis → Write blog post for Momolily"

**Context**: Content marketing for sports bra brand

| # | Sub-Task | Type | Tier | Model | Thinking |
|---|----------|------|------|-------|----------|
| 1 | Scrape competitor data | Web | 1 | `gemini-2.0-flash` | OFF |
| 2 | Extract pricing/features | Data | 2 | `gpt-4.1-nano` | OFF |
| 3 | Analyze market gaps | Analysis | 3 | `claude-3-7-sonnet` | **ON** |
| 4 | Outline blog post | Planning | 3 | `gpt-4o` | OFF |
| 5 | Write full blog post | Creative | 3 | `gpt-4o` | OFF |
| 6 | Generate featured image | Image Gen | 3 | `imagen-3-pro` | OFF |
| 7 | SEO optimization | Analysis | 3 | `claude-3-7-sonnet` | **ON** |

**Execution Flow**:
```
Scraping (Tier 1) → Extract (Tier 2) → Analysis (Tier 3) → Outline (Tier 3) → Write (Tier 3) → Image (Tier 3) → SEO (Tier 3)
```

---

### Summary: Cost Optimization by Workflow

| Workflow | Dominant Tier | Avg Cost |
|----------|--------------|----------|
| Trading Research | Tier 3-4 | Medium |
| TikTok Ad Creation | Tier 2-3 | Low-Medium |
| Robot Debugging | Tier 3-4 | Medium |
| Blog Post | Tier 2-3 | Low-Medium |

**Strategy**: Push heavy reasoning to Tier 3-4, keep browsing/extraction at Tier 1-2.

---

*Note: Adjust model selections based on actual API availability and cost.*
