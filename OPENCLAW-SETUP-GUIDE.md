# 🚀 OpenClaw Full Setup Guide

> A complete, automated OpenClaw deployment for personal AI assistant on Linux (Zorin OS/Ubuntu)

**Author:** Aldo Boendadjaja  
**Platform:** Lenovo ThinkPad X240 (Intel i5-4200U, 8GB RAM)  
**OS:** Zorin OS 17  
**Last Updated:** February 13, 2026

---

## 📋 Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Core Configuration](#core-configuration)
5. [Model Manager System](#model-manager-system)
6. [Memory Integration](#memory-integration)
7. [Automation & Cron Jobs](#automation--cron-jobs)
8. [Security Setup](#security-setup)
9. [Instance Management](#instance-management)
10. [Channel Setup](#channel-setup)
11. [Skills & Workflows](#skills--workflows)
12. [Troubleshooting](#troubleshooting)

---

## Overview

This guide covers a production-ready OpenClaw setup featuring:

- ✅ **5-Tier Model Manager** with automatic routing
- ✅ **Obsidian ↔ Mem0** hybrid memory system
- ✅ **Budget Guard** with silent monitoring
- ✅ **Auto Vision Detection** for image inputs
- ✅ **Agentic Task Manager** for complex workflows
- ✅ **Secure API key management** via `.env`
- ✅ **Single-instance architecture** with systemd

---

## Prerequisites

### Hardware
- **Minimum:** Dual-core CPU, 8GB RAM, 10GB storage
- **Tested:** Lenovo ThinkPad X240 (2014 hardware) ✓
- **OS:** Ubuntu 20.04+ / Zorin OS 17+ / Debian 11+

### Software
```bash
# Required
Node.js v18+     # OpenClaw runtime
Python 3.8+      # Scripts & automation
Git              # Version control
curl             # API calls

# Optional (but recommended)
TLP              # Power management (laptops)
systemd          # Service management
```

### API Keys Required
| Provider | Purpose |获取方式 |
|----------|---------|--------|
| OpenAI | GPT models | platform.openai.com |
| xAI | Grok models | x.ai |
| Google | Gemini models | aistudio.google.com |
| Mem0 | Long-term memory | mem0.ai |
| Brave | Web search | brave.com |

---

## Installation

### 1. Install OpenClaw
```bash
npm install -g openclaw
```

### 2. Initial Configuration
```bash
# Initialize gateway
openclaw gateway start

# Set up completions
source "/home/$USER/.openclaw/completions/openclaw.bash"

# Create workspace
mkdir -p ~/.openclaw/.openclaw/workspace/{scripts,memory,reports,skills}
```

### 3. Directory Structure
```
~/.openclaw/
├── .openclaw/
│   ├── workspace/
│   │   ├── scripts/          # Automation scripts
│   │   ├── memory/          # Daily notes & logs
│   │   ├── reports/         # Generated reports
│   │   └── skills/          # Custom skills
│   └── configs/             # Configuration files
├── bin/
│   └── openclaw-start       # Safe launcher script
└── logs/                    # Gateway logs
```

---

## Core Configuration

### Environment Variables (`.env`)
Create `~/.openclaw/.openclaw/workspace/.env`:

```bash
# AI Providers
OPENAI_API_KEY="sk-..."
XAI_API_KEY="xai-..."
GOOGLE_API_KEY="AIza..."

# Mem0
MEM0_API_KEY="mem0_..."

# Search
BRAVE_API_KEY="..."

# Optional: 1Password
OP_SERVICE_ACCOUNT_TOKEN="..."
```

### Load Environment in Scripts
```python
# scripts/env_loader.py
import os
from dotenv import load_dotenv

load_dotenv("/home/$USER/.openclaw/.openclaw/workspace/.env")
```

---

## Model Manager System

### Concept: 5-Tier Architecture

The Model Manager routes tasks to optimal models based on complexity:

| Tier | Complexity | Use Case | Cost |
|------|------------|----------|------|
| 1 | Ultra-Light | Quick checks, fact retrieval | $0.001 |
| 2 | Light | Standard conversations | $0.01 |
| 3 | Standard | Code, analysis, vision | $0.05 |
| 4 | Capable | Complex reasoning (Thinking ON) | $0.20 |
| 5 | Heavy | Critical tasks, deep research | $1.00+ |

### Model Assignments

**Tier 1 (Ultra-Light)**
```
General:     minimax-m2.1-lightning, gemini-2.0-flash
Vision:      minimax-vision-lightning, gemini-flash-vision
Image Gen:   minimax-image-lightning, stable-diffusion-xl
```

**Tier 2 (Light)**
```
General:     minimax-m2.1, gpt-4.1-nano, claude-3-haiku
Vision:     minimax-vision, gpt-4.1-nano-vision
Image Gen:  flux-schnell, imagen-3
```

**Tier 3 (Standard)**
```
General:     gpt-4o, claude-3-7-sonnet, gemini-2.0-pro
Vision:     gpt-4o-vision, claude-sonnet-vision
Image Gen:  midjourney-v6, dalle-3, imagen-3-pro
```

**Tier 4 (Capable - Thinking ON)**
```
General:     grok-3, claude-3-5-sonnet, gpt-4.5-preview
Vision:     grok-3-vision, claude-5-sonnet-vision
Image Gen:  flux-pro, dalle-3-hd
```

**Tier 5 (Heavy - Critical Only)**
```
General:     claude-4-opus, gpt-4.5, grok-3-think
Vision:     claude-opus-vision, grok-think-vision
Image Gen:  midjourney-v7, dalle-4
```

### Auto Vision Detection
```python
# skills/model-fallback/controller.py

VISION_MODELS = [
    "gpt-4o", "claude-3-7-sonnet",
    "gemini-1.5-pro", "gemini-1.5-flash"
]

def has_image_input(messages):
    """Detect if input contains images."""
    for msg in messages:
        if msg.get("content"):
            if isinstance(msg["content"], list):
                for item in msg["content"]:
                    if item.get("type") == "image_url":
                        return True
    return False

def select_model(messages, complexity):
    """Route to vision or text model based on input."""
    if has_image_input(messages):
        return get_best_vision_model(complexity)
    return get_best_text_model(complexity)
```

### Fallback Chain
```
Tier 1 → Tier 2 → Tier 3 → Tier 4 → Tier 5
```

If primary provider fails, automatically escalates to next tier.

---

## Memory Integration

### Hybrid System: Obsidian + Mem0

**Strategy:**
- **Read:** Obsidian (unlimited local access)
- **Write:** Mem0 (1x/day, semantic search)

### Setup

#### 1. Install Mem0 CLI
```bash
pip install mem0ai
```

#### 2. Quota Tracker
```python
# scripts/quota-tracker.py

DAILY_LIMIT = 33  # Mem0 free tier

def track_usage():
    state = load()
    today = datetime.now().strftime("%Y-%m-%d")
    
    # Reset on new day, roll over unused
    if state["date"] != today:
        unused = max(0, DAILY_LIMIT - state["used"])
        return {"used": 0, "rollover": unused}
    
    # Increment on search
    if state["used"] < DAILY_LIMIT + state["rollover"]:
        state["used"] += 1
        save(state)
    
    return state
```

#### 3. Mem0 Controller
```python
# scripts/mem0/controller.py

USER_ID = "aldow3n@gmail.com"

def add_memory(messages, metadata=None):
    """Add memory to Mem0."""
    payload = {
        "messages": messages,
        "user_id": USER_ID,
        "version": "v2"
    }
    if metadata:
        payload["metadata"] = metadata
    return curl_post("/memories/", payload)

def search_memories(query, limit=5):
    """Search with automatic quota tracking."""
    if not can_use_mem0():
        return {"error": "quota_exceeded", "source": "Obsidian"}
    
    results = curl_post("/memories/search/", {
        "query": query,
        "user_id": USER_ID,
        "limit": limit
    })
    
    if "error" not in results:
        increment_quota()
    
    return results
```

#### 4. Daily Sync Cron
```bash
# Runs daily at 23:50 WIB
50 23 * * * python3 ~/bin/obsidian-sync.py
```

### Memory Workflow
```
1. User query → Check Mem0 (if quota available)
2. If quota exceeded → Fallback to Obsidian
3. Important memories → Auto-add to Mem0 (daily)
4. Full context → Persist in daily notes
```

---

## Automation & Cron Jobs

### Session Summarizer
```python
# scripts/session-summarizer.py

def summarize_session(session_key):
    """Extract key points from session transcript."""
    transcript = load_transcript(session_key)
    
    # Parse messages
    messages = extract_messages(transcript)
    
    # Generate summary
    summary = {
        "key_accomplishments": [],
        "files_created": [],
        "decisions": [],
        "open_items": [],
        "model_used": get_model_from_session(session_key)
    }
    
    for msg in messages:
        if is_assistant_response(msg):
            summary["key_accomplishments"].extend(
                extract_action_items(msg)
            )
    
    return summary
```

### Daily Aggregator
```python
# scripts/daily-aggregator.py

def aggregate_daily():
    """Combine all sessions → push to Obsidian."""
    sessions = get_today_sessions()
    
    combined = {
        "date": today(),
        "sessions": [],
        "total_cost": calculate_cost(sessions),
        "model_usage": count_model_usage(sessions),
        "highlights": extract_highlights(sessions)
    }
    
    for session in sessions:
        combined["sessions"].append(
            summarize_session(session)
        )
    
    # Push to Obsidian
    save_to_obsidian(combined, f"daily-notes/{today()}.md")
    
    return combined
```

### Cron Jobs (via OpenClaw)
```bash
# Daily Mem0 Sync (23:50 WIB)
openclaw cron add \
  --name "Daily Mem0 Sync" \
  --schedule "cron 50 23 * * *" \
  --agent main \
  --payload '{
    "kind": "agentTurn",
    "message": "Sync recent context to Mem0"
  }'

# Daily Session Aggregation (23:59 WIB)
openclaw cron add \
  --name "Daily Session Aggregation" \
  --schedule "cron 59 23 * * *" \
  --agent main \
  --payload '{
    "kind": "agentTurn",
    "message": "Run daily-aggregator.py"
  }'
```

### Budget Guard
```python
# scripts/budget-guard.py

DAILY_BUDGET = 2.00  # USD
WARNING_THRESHOLD = 0.80  # 80%

def check_budget():
    usage = get_today_usage()
    percentage = usage / DAILY_BUDGET
    
    if percentage >= WARNING_THRESHOLD:
        send_alert(f"⚠️ Budget at {percentage:.1%}")
    else:
        log(f"✅ Budget healthy: {percentage:.1%}")
```

---

## Security Setup

### API Key Protection
```bash
# .gitignore
.env
.env.*
*.log
memory/*.jsonl
*.sock
```

### Environment Template
```bash
# .env.template (SAFE to commit)
# Fill in your keys:

OPENAI_API_KEY=""
XAI_API_KEY=""
GOOGLE_API_KEY=""
MEM0_API_KEY=""
BRAVE_API_KEY=""
OP_SERVICE_ACCOUNT_TOKEN=""
```

### Secure Loading
```python
import os
from pathlib import Path

ENV_PATH = Path.home() / ".openclaw" / ".openclaw" / "workspace" / ".env"

if ENV_PATH.exists():
    from dotenv import load_dotenv
    load_dotenv(ENV_PATH)
    print("✅ API keys loaded")
else:
    print("⚠️ .env not found")
```

### Key Rotation Protocol
If keys are exposed:
1. Rotate API keys on provider dashboard
2. Update `.env`
3. Delete `api.md` from Obsidian
4. Clear git history if committed
5. Enable 1Password for future

---

## Instance Management

### Safe Launcher Script
```bash
#!/bin/bash
# ~/bin/openclaw-start

# Kill existing duplicates
pkill -f "openclaw.*gateway" || true
pkill -f "openclaw$" || true

# Start fresh gateway
openclaw gateway start

echo "✅ OpenClaw started fresh"
```

### Systemd Service
```ini
# ~/.config/systemd/user/openclaw-gateway.service

[Unit]
Description=OpenClaw Gateway
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/node /home/$USER/.npm-global/lib/node_modules/openclaw/dist/index.js gateway
Restart=always
RestartSec=10

[Install]
WantedBy=default.target
```

### Enable Service
```bash
# Install
systemctl --user daemon-reload
systemctl --user enable openclaw-gateway.service

# Control
systemctl --user start openclaw-gateway
systemctl --user stop openclaw-gateway
systemctl --user status openclaw-gateway
```

### Alias for Convenience
```bash
# ~/.bashrc
alias openclaw='~/bin/openclaw-start'
```

### Check Instances
```bash
# All processes
pgrep -a openclaw

# Full details
ps aux | grep openclaw

# Port binding
lsof -i :18789
```

---

## Channel Setup

### Telegram (Primary)
```bash
# Create bot via @BotFather
# Get bot token

# Configure OpenClaw
openclaw config set \
  --channel telegram \
  --bot-token "YOUR_BOT_TOKEN" \
  --dm-policy pairing \
  --group-policy allowlist
```

### Adding More Channels
| Channel | Setup Command |
|---------|---------------|
| WhatsApp | `openclaw config set --channel whatsapp ...` |
| Discord | `openclaw config set --channel discord ...` |
| Google Chat | `openclaw config set --channel googlechat ...` |

### Architecture Note
> **One gateway serves all channels.** No need for multiple instances.

---

## Skills & Workflows

### Agentic Task Manager
```markdown
# skills/agentic-task-manager/SKILL.md

## Manager Pattern
1. Decompose complex task into subtasks
2. Assign optimal model per subtask (via Model Manager)
3. Spawn isolated subagents
4. Synthesize results

## Usage
{{ag-task-manager
  task="Research IDX stocks for swing trade"
  subtasks=[
    {"step": "Screen", "complexity": 1},
    {"step": "Analyze", "complexity": 4},
    {"step": "Summarize", "complexity": 2}
  ]
}}
```

### Multi-Model Workflows

**1. IDX Stock Research → Signal**
```
Tier 1 (MiniMax) → Screen stocks by liquidity
Tier 3 (Claude)  → Technical analysis
Tier 4 (Grok)    → Deep reasoning on signals
Tier 4 (Claude)  → Risk assessment
Tier 2 (MiniMax) → Format recommendation
```

**2. TikTok Ad Creation**
```
Tier 1 (MiniMax) → Check product data
Tier 3 (GPT-4o) → Script writing
Tier 3 (Claude) → Visuals brief
Tier 3 (DALL-E) → Generate thumbnails
Tier 2 (MiniMax) → Finalize copy
```

**3. Koda Robot Debug**
```
Tier 1 (MiniMax) → Quick ESP32 check
Tier 4 (Grok)    → Motor driver analysis
Tier 4 (Claude)  → Pin configuration
Tier 4 (Claude) → Android integration review
Tier 3 (Claude)  → Code suggestions
Tier 2 (MiniMax) → Summary
```

---

## Troubleshooting

### Common Issues

#### 1. Multiple Gateway Instances
```bash
# Check
pgrep -a openclaw

# Kill all
pkill -f "openclaw.*gateway" || true
pkill -f "openclaw$" || true

# Restart fresh
~/bin/openclaw-start
```

#### 2. API Keys Not Loading
```bash
# Verify .env exists
cat ~/.openclaw/.openclaw/workspace/.env

# Check permissions
ls -la ~/.openclaw/.openclaw/workspace/.env

# Reload
source ~/.bashrc
```

#### 3. Mem0 Quota Exceeded
```
Symptom: Memory searches fallback to Obsidian

Solution: Wait for rollover (next day)
         Or: Delete old memories in Mem0 dashboard
```

#### 4. Cron Jobs Not Running
```bash
# List cron jobs
openclaw cron list

# Check status
openclaw cron status

# View logs
openclaw cron runs --id <job-id>
```

#### 5. Telegram Not Responding
```bash
# Check gateway
systemctl --user status openclaw-gateway

# Restart gateway
systemctl --user restart openclaw-gateway

# Check token
openclaw config get --channel telegram
```

### Performance Tuning

#### Thermal Management (Laptops)
```bash
# Install TLP
sudo apt install tlp tlp-rdw

# Start
sudo tlp start

# Check temps
sensors | grep -i temp
```

#### CPU Governor
```bash
# Check current
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Set to ondemand (balance power/performance)
echo ondemand | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
```

---

## Files Reference

| File | Location | Purpose |
|------|----------|---------|
| `ENABLED_MODELS.md` | `~/.openclaw/` | Model tier configuration |
| `.env` | `~/.openclaw/.openclaw/workspace/` | API keys (SECRET) |
| `.env.template` | `~/.openclaw/.openclaw/workspace/` | Template for .env |
| `openclaw-start` | `~/bin/` | Safe launcher script |
| `session-summarizer.py` | `~/.openclaw/.openclaw/workspace/scripts/` | Session summarization |
| `daily-aggregator.py` | `~/.openclaw/.openclaw/workspace/scripts/` | Daily consolidation |
| `quota-tracker.py` | `~/.openclaw/.openclaw/workspace/scripts/` | Mem0 quota tracking |
| `budget-guard.py` | `~/.openclaw/.openclaw/workspace/scripts/` | Budget monitoring |
| `controller.py` | `~/.config/openclaw/skills/mem0-sync/` | Mem0 integration |
| `SOUL.md` | `~/.openclaw/.openclaw/workspace/` | AI persona configuration |
| `USER.md` | `~/.openclaw/.openclaw/workspace/` | User preferences |

---

## Budget Summary

| Metric | Value |
|--------|-------|
| Daily Limit | $2.00 USD |
| Current Usage | ~$0.025 (1.25%) |
| Threshold (Warning) | $1.60 (80%) |
| Status | ✅ Healthy |

---

## Changelog

### February 13, 2026
- Added Mem0 quota rollover logic
- Fixed budget guard cron jobs
- Implemented auto vision detection
- Created Agentic Task Manager skill
- Designed Momolily content pipeline
- Security hardening (API key protection)
- Added safe launcher script

### February 12, 2026
- Initial Model Manager 5-tier system
- Multi-Model Router implementation
- Obsidian ↔ Mem0 integration
- GitHub repo published
- Budget Guard cron setup

---

## License

MIT License - Feel free to fork and customize.

---

**Questions? Issues? Pull requests welcome!**
