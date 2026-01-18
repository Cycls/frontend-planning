# AgentUI: Thinking & Steps
**Week of Jan 20, 2026**

---

## Goal

Perfect the "agent is working" experience. User should always know what's happening.

**Reference:** Claude Web UI, Claude Code CLI, [prompt-kit Chain of Thought](https://www.prompt-kit.com/docs/chain-of-thought)

---

## 1. Layout

```
┌─────────────────────────────────────────────────────┐
│ Analyzing your request...                    3.2s ∨ │  ← THINKING BAR
└─────────────────────────────────────────────────────┘

I'll research the tourism industry for you...          ← RESPONSE TEXT

┌─────────────────────────────────────────────────────┐
│ ∨  6 steps                                          │  ← STEPS (last 2 visible)
│ ├─ ● +4 more steps                                  │
│ ├─ ● Searched codebase                              │
│ └─ ● Fetched docs.example.com                       │
└─────────────────────────────────────────────────────┘
```

---

## 2. Thinking Bar

Already exists. Shows current activity with timer, collapsible.

```
┌─────────────────────────────────────────────────────┐
│ Analyzing your request...                    3.2s ∨ │
└─────────────────────────────────────────────────────┘
```

**Protocol:**
```
data: {"type": "thinking", "thinking": "Analyzing your request..."}

data: {"type": "thinking", "thinking": "Done.", "done": true}
```

---

## 3. Steps

### Status Indicators
- ○ = running (circle, unfilled)
- ● = completed (filled)

### Default (shows last 2 steps)
```
┌─────────────────────────────────────────────────────┐
│ ∨  6 steps                                          │
│ │                                                   │
│ ├─ ● +4 more steps                                  │  ← older grouped
│ │                                                   │
│ ├─ ● Searched codebase                 10 results ∨ │
│ │                                                   │
│ └─ ● Fetched docs.example.com                    ∨  │
└─────────────────────────────────────────────────────┘
```

### Expanded (all steps visible)
```
┌─────────────────────────────────────────────────────┐
│ ∧  6 steps                                          │
│ │                                                   │
│ ├─ ● Read src/auth.ts                            ∨  │
│ ├─ ● Analyzed dependencies                       ∨  │
│ ├─ ● Checked imports                             ∨  │
│ ├─ ● Validated types                             ∨  │
│ ├─ ● Searched codebase                 10 results ∨ │
│ └─ ● Fetched docs.example.com                    ∨  │
└─────────────────────────────────────────────────────┘
```

### Requirements
- Show last 2 steps by default, group older as "+N more steps"
- Header: "N steps" with collapse toggle
- Vertical line connecting steps (hide on last)
- ○ circle = running, ● filled = complete
- Each step expandable for nested data (chevron)
- Optional result on right side
- Group completed steps: show "+N more steps" + last 2

### Protocol
```
data: {"type": "step", "step": "Read src/auth.ts", "data": "contents or details here..."}

data: {"type": "step", "step": "Searched codebase", "result": "10 results", "data": "match1, match2..."}

data: {"type": "step", "step": "Fetching docs.example.com"}
```

- `step` = description (required)
- `result` = summary shown on right (optional)
- `data` = nested content shown when step expanded (optional)
- Step is "running" until next step arrives or response starts

---

## 4. Flow

```
User sends message
        ↓
┌──────────────────────────────────┐
│ Analyzing...                0.5s │
└──────────────────────────────────┘
        ↓
┌──────────────────────────────────┐
│ ∨  1 step                        │
│ └─ ○ Reading src/auth.ts         │  ← running (circle)
└──────────────────────────────────┘
        ↓ (step completes, next starts)
┌──────────────────────────────────┐
│ ∨  2 steps                       │
│ ├─ ● Read src/auth.ts            │  ← complete (filled)
│ └─ ○ Searching codebase          │  ← running
└──────────────────────────────────┘
        ↓ (more steps, shows last 2)
┌──────────────────────────────────┐
│ ∨  4 steps                       │
│ ├─ ● +2 more steps               │  ← grouped
│ ├─ ● Validated types             │
│ └─ ○ Searching codebase          │  ← running
└──────────────────────────────────┘
        ↓ (all done)
┌──────────────────────────────────┐
│ ∨  6 steps                       │
│ ├─ ● +4 more steps               │
│ ├─ ● Searched codebase           │
│ └─ ● Fetched docs                │
└──────────────────────────────────┘

Here's what I found...
```

---

## 5. This Week

- [ ] **Steps Container** — show last 2 by default, expand to show all
- [ ] **Step Item** — ○ running / ● complete, expandable nested data
- [ ] **Grouping** — "+N more steps" for older steps
- [ ] **Test flow**

---

*Last updated: Jan 18, 2026*
