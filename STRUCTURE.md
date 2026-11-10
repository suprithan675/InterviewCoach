# Java Interview Roadmap — Structure Guide

**File:** `java-interview-roadmap.html`  
**Type:** Single-file HTML+CSS+JS app — no build step, no server required.  
**Storage:** `localStorage` key `jip:v1`; theme preference in `jip:theme`  
**Content:** 21 topics · 228 concepts · 318 interview questions · 66 scenarios · 54 coding exercises

---

## Architecture Overview

```
<html data-theme="light|dark">
└── <body>  ← animated gradient background (25s loop)
    ├── #sb           Sidebar (fixed left, 280px, frosted-glass)
    │   ├── .sb-top     Logo + theme toggle button
    │   ├── .sb-search  Search input (filters sidebar topics live)
    │   ├── .sb-nav     Dashboard / Review Queue / Quick Drill / Backup links
    │   ├── .sb-topics  Phase groups + topic links (rendered by renderSb())
    │   └── .sb-footer  Overall progress bar
    ├── #sb-ov        Overlay (mobile only, closes sidebar on tap)
    └── #main
        ├── #topbar   Page title + action buttons (frosted-glass)
        └── #page     Dynamic content — re-rendered on every route change
```

**Routing:** Hash-based. `go(route)` sets `location.hash` and re-renders `#page`.  
**Routes:** `'home'` · `'review'` · `'drill'` · `'drill?t=<topicId>'` · `'backup'` · `'<topicId>'`

---

## State Object

```javascript
let S = {
  topicStatus: {},    // { [topicId]: 'not_started'|'studying'|'done'|'review' }
  practiced:   {},    // { [qk(tid, questionText)]: true }
  flagged:     {},    // { [qk(tid, questionText)]: true }   ← questions/scenarios
                      // { [ck(tid, conceptTerm)]: true }    ← concepts (prefix 'cf:')
  notes:       {},    // { [topicId]: "user note string" }
};
```

**Key functions:**
- `qk(tid, txt)` → `"<tid>:<first55chars-hyphenated>"` — storage key for questions/scenarios
- `ck(tid, term)` → `"cf:<tid>:<first30chars-underscored>"` — storage key for concept flags
- `save()` → `localStorage.setItem('jip:v1', JSON.stringify(S))`

**Status auto-computation** (`computeStatus(tid)` — called after every `togglePrac()`):
- `not_started` → 0 questions + scenarios practiced
- `studying` → 1+ practiced, but not all
- `done` → all questions + scenarios practiced (also sets if previous state was `done`)
- `review` → manual only; set/cleared via `toggleReview(tid)`  
  Note: if status is `review` and all questions get practiced, it stays `review` (not reset to `done`)

---

## Topic Data Format

Topics live in `const TOPICS = [...]`. Order controls sidebar display order.

```javascript
{
  id:       'java-basics',           // unique lowercase-kebab-case slug → used as route + storage key
  phase:    'core',                  // must match a phase id: 'core'|'spring'|'databases'|'messaging'|'practice'
  title:    'Java Basics & OOP',     // displayed in sidebar and topbar
  tag:      'Fundamentals',          // shown as an uppercase gradient pill badge on topic page
  level:    'basic',                 // shown as plain text next to tag: 'basic'|'intermediate'|'advanced'
  summary:  'One-sentence overview shown below the title.',

  concepts: [
    // Two-element form (most topics): [term, definition]
    ["Polymorphism", "One interface, many forms — overloading and overriding."],

    // Three-element form (with bullet highlights): [term, definition, [point, ...]]
    // Currently only java-basics uses this; add to others when needed
    ["Encapsulation", "Bundle data and methods; expose via getters/setters.", [
      "Always use private fields",
      "Validate in setters to prevent invalid state",
      "Defensive copies for mutable fields (Date, arrays, List)",
    ]],
    // Target: 8–14 concepts per topic
  ],

  questions: [
    // Format: [questionText, answerText]
    ["What are the four pillars of OOP?", "Encapsulation bundles data..."],
    // Target: 10–20 questions per topic
  ],

  scenarios: [
    // Same format as questions — rendered under "Scenario Questions" section
    ["Design a payment system: Credit Card, PayPal, Bank Transfer.", "Define a PaymentMethod interface..."],
    // Target: 3–8 scenarios per topic
  ],

  coding: [
    // Format: [problemTitle, explanationText, codeString]
    // Use backtick template literals for codeString (double-quote chars don't need escaping)
    ["Implement a thread-safe Singleton.", "Static inner class loaded lazily.", `
public final class AppConfig {
    private AppConfig() {}
    private static class Holder { static final AppConfig INSTANCE = new AppConfig(); }
    public static AppConfig getInstance() { return Holder.INSTANCE; }
}`],
    // Target: 2–5 coding examples per topic
  ],
}
```

**Important escaping note:** Question/answer strings use double-quoted JS strings `"..."`.  
Any `\"` inside them will terminate the string — use `\\\"` or rephrase to avoid it.  
Code blocks use backtick template literals `` `...` `` — safe for all Java code.

---

## Phases

| ID | Title | Icon | Sidebar border | Topic header tint |
|----|-------|------|----------------|-------------------|
| `core` | Core Java | ☕ | `#f97316` orange | orange gradient |
| `spring` | Spring & APIs | 🌱 | `#16a34a` green | green gradient |
| `databases` | Databases | 🗄️ | `#9333ea` purple | purple gradient |
| `messaging` | Messaging | 📨 | `#0891b2` cyan | cyan gradient |
| `practice` | Interview Practice | 🎯 | `#db2777` pink | pink gradient |

---

## All 21 Topics

### Core Java — 9 topics

| ID | Title | Tag | Level | Q | S | Code |
|----|-------|-----|-------|---|---|------|
| `java-basics` | Java Basics & OOP | Fundamentals | basic | 17 | 5 | 3 |
| `strings` | Strings | Core Java | basic | 15 | 3 | 3 |
| `collections` | Collections Framework | Data Structures | intermediate | 18 | 5 | 3 |
| `exceptions-generics` | Exceptions & Generics | Core Java | intermediate | 15 | 3 | 3 |
| `java8` | Java 8 Features | Modern Java | intermediate | 15 | 3 | 3 |
| `streams` | Streams API | Functional Java | intermediate | 16 | 3 | 3 |
| `concurrency` | Multithreading & Concurrency | Advanced | advanced | 20 | 5 | 3 |
| `jvm-gc` | JVM Memory & GC | Advanced | advanced | 15 | 3 | 2 |
| `modern-java` | Modern Java (9–21) | Modern Java | intermediate | 15 | 3 | 3 |

### Spring & APIs — 5 topics

| ID | Title | Tag | Level | Q | S | Code |
|----|-------|-----|-------|---|---|------|
| `spring-core` | Spring Core & AOP | Spring | intermediate | 16 | 3 | 2 |
| `spring-boot` | Spring Boot | Spring | intermediate | 15 | 3 | 2 |
| `rest-api` | REST API Design | API | intermediate | 15 | 3 | 2 |
| `microservices` | Microservices | Architecture | advanced | 15 | 3 | 2 |
| `hibernate-jpa` | Hibernate & Spring Data JPA | Persistence | advanced | 15 | 3 | 3 |

### Databases — 2 topics

| ID | Title | Tag | Level | Q | S | Code |
|----|-------|-----|-------|---|---|------|
| `sql` | SQL | Databases | intermediate | 15 | 3 | 3 |
| `mongodb` | MongoDB | Databases | intermediate | 14 | 3 | 2 |

### Messaging — 1 topic

| ID | Title | Tag | Level | Q | S | Code |
|----|-------|-----|-------|---|---|------|
| `kafka` | Apache Kafka | Messaging | advanced | 14 | 3 | 2 |

### Interview Practice — 4 topics

| ID | Title | Tag | Level | Q | S | Code |
|----|-------|-----|-------|---|---|------|
| `testing` | Testing (JUnit & Mockito) | Quality | intermediate | 14 | 2 | 3 |
| `design-patterns` | Design Patterns & SOLID | Architecture | intermediate | 13 | 3 | 2 |
| `coding-round` | Coding Round Problems | Algorithms | intermediate | 15 | 2 | 4 |
| `behavioral` | Behavioral Round | Soft Skills | basic | 11 | 2 | 1 |

**Q** = interview questions · **S** = scenario questions · **Code** = coding exercises

---

## How to Add a New Topic

1. **Pick a phase ID** from the 5 above. Place the new topic object in `const TOPICS = [...]` after the last topic in that phase.

2. **Add the topic object** (copy this template):

```javascript
{
  id: 'your-topic-id',          // unique, lowercase-kebab-case
  phase: 'core',                // must be one of the 5 phase IDs
  title: 'Your Topic Title',
  tag: 'Tag Label',             // 1–2 words; CSS uppercases it automatically
  level: 'intermediate',        // 'basic' | 'intermediate' | 'advanced'
  summary: 'One sentence summary of what this topic covers.',
  concepts: [
    ["Term", "Definition — keep under 30 words."],
    // Add ["Term", "Def", ["point1", "point2"]] to include review bullet points
    // 8–14 concepts ideal
  ],
  questions: [
    ["Question?", "Answer — 3–8 comprehensive sentences."],
    // 10–20 questions ideal
  ],
  scenarios: [
    ["Scenario / design problem?", "Solution approach."],
    // 3–8 scenarios ideal
  ],
  coding: [
    ["Problem title", "Brief approach note", `Java code here`],
    // 2–5 items ideal
  ],
},
```

3. **No other code changes needed.** The sidebar, routing, progress tracking, storage, search, drill, and review queue all pick it up automatically.

4. **To add a new phase** (not just a topic):
   - Add `{id:'newphase', title:'New Phase Name', icon:'🔧'}` to `const PHASES = [...]`
   - Add CSS variables in `:root`: `--phase-newphase:#hex; --phase-newphase-s:#hex;`
   - Add sidebar border: `.ph-hdr[data-phase="newphase"]{border-left-color:var(--phase-newphase)}`
   - Add topic header gradient: `.topic-hdr[data-phase="newphase"]{background:linear-gradient(135deg,rgba(r,g,b,.12),transparent)}`

---

## Key CSS Variables (`:root`)

```css
/* Surfaces */
--bg            page background (now transparent — body has animated gradient)
--surface       card/panel base color
--surface2      secondary surface (inputs, code blocks)
--border        border/separator color

/* Typography */
--text          primary text
--text2         secondary text
--text3         muted/placeholder text

/* Semantic colors */
--primary, --primary-s          blue accent + tinted background
--success, --success-s          green (practiced, done)
--warning, --warning-s          amber (flagged, review)
--danger, --danger-s            red (destructive actions)

/* Gradients */
--grad-primary   linear-gradient(135deg, #2563eb, #7c3aed)   /* blue→purple */
--grad-success   linear-gradient(135deg, #16a34a, #059669)   /* green gradient */

/* Phase colors (each has -s soft variant) */
--phase-core, --phase-core-s         orange
--phase-spring, --phase-spring-s     green
--phase-databases, --phase-databases-s  purple
--phase-messaging, --phase-messaging-s  cyan
--phase-practice, --phase-practice-s    pink

/* Layout */
--sb-w    sidebar width (280px)
--tb-h    topbar height (56px)
--r       border-radius (10px)
--tr      transition duration (200ms ease)

/* Elevation */
--shadow        subtle card shadow
--shadow-md     medium shadow
--shadow-hover  hover-state lift shadow
```

---

## Feature Reference

| Feature | Trigger | Function | Storage |
|---------|---------|----------|---------|
| **Status badge** | Automatic after any ✓ toggle | `computeStatus(tid)` | `S.topicStatus[tid]` |
| **Mark for review** | Button on topic page (shown when `done` or `review`) | `toggleReview(tid)` | `S.topicStatus[tid] = 'review'` |
| **Practiced ✓** | Checkbox on each question | `togglePrac(tid, txt, el)` | `S.practiced[qk(tid,txt)]` |
| **Question flag 🚩** | ⚑ button on each question | `toggleFlag(tid, txt, el)` | `S.flagged[qk(tid,txt)]` |
| **Concept flag 🚩** | ⚑ button on each concept card (hover to reveal) | `toggleConceptFlag(tid, term, el)` | `S.flagged[ck(tid,term)]` |
| **Question filter** | All / Practiced / Not Practiced / Flagged buttons | `fltQ(el, type)` | DOM only (no storage) |
| **Concept filter** | All / 🚩 Flagged buttons | `fltConcepts(el, type)` | DOM only (no storage) |
| **Quick Drill** | ⚡ Drill button | `renderDrill()` / `startDrill()` | Updates `S.practiced` live |
| **Review Queue** | ⭐ Review Queue link | `renderReview()` | Reads `S.flagged` + `S.topicStatus` |
| **Search** | Sidebar search input | `sbSearch(q)` | DOM only |
| **Notes** | Textarea on topic page | `saveNotes(tid, val)` | `S.notes[tid]` |
| **Dark mode** | 🌙 button in sidebar | `toggleTheme()` | `localStorage['jip:theme']`; `data-theme` on `<html>` |
| **Backup export** | Backup page | JSON stringify of `S` | — |
| **Backup import** | Backup page | `Object.assign(S, parsed)` | Overwrites `S` and saves |
| **Reset** | Backup page | `doReset()` | Clears all of `S` |

---

## Visual Design Notes

| Element | Style |
|---------|-------|
| Body background | Animated gradient (25s loop): `#eef2ff → #fdf4ff → #ecfdf5 → #fef9c3` |
| Dark mode background | Animated gradient: `#0f172a → #1e0a2e → #0a1f1a → #1a1500` |
| Sidebar + topbar | Frosted glass: `rgba(255,255,255,0.88)` + `backdrop-filter: blur(20px)` |
| Topic page header | Phase-colored gradient tint (`rgba(phase-color, 0.12)` → transparent) |
| Concept card top borders | Cycle through 5 phase colors via `nth-child(5n+1..5)` |
| Sidebar phase headers | Colored left border (`border-left-color: var(--phase-X)`) |
| Active sidebar link | Inset left shadow: `box-shadow: inset 3px 0 0 var(--primary)` |
| Practiced question | Green left border: `.q-item.prac { border-left: 3px solid var(--success) }` |
| Flagged question | Amber left border: `.q-item.flg { border-left: 3px solid var(--warning) }` |
| Flagged concept | Amber left border: `.concept-card.c-flagged { border-left: 3px solid var(--warning) }` |
| Studying status dot | Pulsing animation (`@keyframes pulse-dot`) |
| Page navigation | Fade-in + slide-up animation (`@keyframes fade-in`) |
| Gradient elements | Logo text, primary buttons, stat numbers, t-tag badges, ci-num circles |
