# Applekid Pi Display — Research Doc
*Personal Assistant Dashboard on Raspberry Pi*

---

## Overview

A dedicated Raspberry Pi with a small screen that serves as:
- Visual presence indicator (am I working/idle/sleeping?)
- Notification hub for completed tasks and pending requests
- Pomodoro timer with physical input
- Calendar glanceable display
- Learning tool (Rust flashcards)

---

## Hardware Considerations

### Screen Options

| Screen | Size | Resolution | Notes |
|--------|------|------------|-------|
| Pimoroni HyperPixel 4.0 | 4" | 800x480 | Touch, high DPI, great colors |
| Waveshare 3.5" | 3.5" | 480x320 | Affordable, decent |
| Pimoroni Inky Impression | 5.7" | 600x448 | E-ink, 7-color, low power, no backlight |
| Official Pi 7" Touchscreen | 7" | 800x480 | Larger, good for more info |

**Recommendation:** HyperPixel 4.0 for animations/face, or Inky Impression if we want always-on low-power display with a more retro/calm aesthetic.

### Input Device Options

| Device | Type | Pros | Cons |
|--------|------|------|------|
| **Wireless Numpad** | Bluetooth/2.4GHz | Compact, dedicated buttons, cheap | Limited keys |
| **Macro Pad** (Elgato Stream Deck Mini) | USB | Customizable LCD keys, tactile | Expensive ($80+) |
| **DIY Macro Pad** (Pi Pico + keys) | USB | Fully custom, cheap | Requires building |
| **Small Wireless Keyboard** | Bluetooth | Full input capability | Bulky for desk |
| **Single Arcade Button** (for Pomodoro) | GPIO | Satisfying, dedicated | Very limited |

**Recommendation:** 
- **Primary:** Cheap wireless numpad (~$15) — map keys to functions
- **Bonus:** Single big arcade button wired to GPIO for Pomodoro start/stop (satisfying physical interaction)

---

## Features Deep Dive

### 1. Animated Face / Status Indicator

A simple animated character (pixel art or vector) that shows my current state.

**States:**
| State | Visual | Meaning |
|-------|--------|---------|
| 🟢 **Awake** | Eyes open, occasional blink | Ready, monitoring |
| 🔵 **Working** | Focused expression, maybe sparkles/gears | Actively processing a task |
| 😴 **Sleeping** | Eyes closed, Zzz animation | Idle, heartbeat mode |
| 🔔 **Attention** | Eyebrows raised, subtle pulse | Needs your input |
| ✅ **Done** | Happy expression, checkmark | Just completed something |

**Implementation Ideas:**
- Sprite sheet animation (pixel art aesthetic, EarthBound vibes)
- SVG/Lottie animations (smoother, scalable)
- State machine controls transitions

**Inspiration:**
- Tamagotchi (simple, expressive with minimal pixels)
- Cozmo robot (expressive eyes)
- Animal Crossing character reactions

### 2. Task/Request Notifications

Display completed tasks and outstanding requests.

**Layout concept:**
```
┌─────────────────────────────────┐
│  [FACE]     │  📋 PENDING       │
│             │                   │
│   (◕‿◕)    │  • Vercel access  │
│             │  • gh auth scope  │
│  "Working"  │                   │
├─────────────┴───────────────────┤
│  ✅ COMPLETED (tap to dismiss)  │
│  • ETH History deployed         │
│  • CI workflow ready            │
└─────────────────────────────────┘
```

**Data Flow:**
1. I track pending requests in a file (`~/.applekid/pending.json`)
2. When I complete something, I add to completed list
3. Pi polls this file (or receives push via Tailscale)
4. Display updates, plays subtle notification sound/animation

### 3. Calendar Glance

Show upcoming events (next 2-3).

```
┌─────────────────────────────────┐
│  📅 NEXT UP                     │
│                                 │
│  2:00 PM  Team standup    [30m] │
│  4:30 PM  1:1 with Sarah  [45m] │
│                                 │
└─────────────────────────────────┘
```

**Integration:**
- Google Calendar API (need OAuth setup)
- Or: I fetch calendar via `gog calendar` and write to shared file
- Pi reads and displays

### 4. Pomodoro Timer

Physical button triggers Pomodoro flow.

**Flow:**
1. Press button → Start 25-min work session
2. Display shows countdown, face is "focused"
3. Timer ends → Chime, face changes to "break time"
4. 5-min break countdown
5. After 4 pomodoros → Longer break (15-30 min)

**Visual:**
```
┌─────────────────────────────────┐
│                                 │
│         🍅 POMODORO             │
│                                 │
│           18:42                 │
│         ━━━━━━━━━░░░            │
│                                 │
│     Session 2 of 4              │
│                                 │
└─────────────────────────────────┘
```

**Button Interaction:**
- Single press: Start/pause
- Double press: Skip to break
- Long press: Reset

### 5. Rust Flashcards / Quiz

Periodic display of Rust learning content.

**Modes:**
- **Passive:** Show a concept/tip every N minutes when idle
- **Active:** Press button to start quiz mode

**Card Types:**
```
┌─────────────────────────────────┐
│  🦀 RUST TIP                    │
│                                 │
│  What does `?` do?              │
│                                 │
│  [Press any key to reveal]      │
│                                 │
└─────────────────────────────────┘

[after keypress]

┌─────────────────────────────────┐
│  🦀 RUST TIP                    │
│                                 │
│  `?` is the "try" operator.     │
│  It propagates errors up,       │
│  equivalent to:                 │
│                                 │
│  match result {                 │
│    Ok(v) => v,                  │
│    Err(e) => return Err(e),     │
│  }                              │
└─────────────────────────────────┘
```

**Content Sources:**
- Rust Book key concepts
- Common gotchas
- `rustlings` exercise hints
- Clippy lints explained

### 6. Additional Ideas

**Weather glance:**
```
│  ☀️ 72°F  │  Sunny, clear skies  │
```

**Current task context:**
Show what I'm actively working on (from a task file or last message).

**Spotify/music display:**
What's playing (if integrated).

**System status:**
- Gateway online/offline
- Last message received time
- Token usage today

---

## UI Design Concepts

### Theme: Retro Terminal + Pixel Art

Given the EarthBound aesthetic we've established, lean into:
- Pixel art face/icons
- Monospace fonts for data
- Dark background with accent colors
- Subtle scanline effect (optional)

### Color Palette

```
Background:  #0A0A0B (near black)
Primary:     #627EEA (Ethereum purple)
Accent:      #00D395 (success green)
Warning:     #FF6B35 (orange)
Text:        #FFFFFF
Muted:       #71717A
```

### Layout Options

**Option A: Face-Focused**
Face is the hero, info tucked around edges.
```
┌─────────────────────────────────┐
│ 2:30 PM          ☀️ 72°F       │
├─────────────────────────────────┤
│                                 │
│           (◕‿◕)                │
│          Working               │
│                                 │
├─────────────────────────────────┤
│ 📋 Pending: 2    ✅ Done: 3     │
└─────────────────────────────────┘
```

**Option B: Dashboard Grid**
Information-dense, face is one tile among many.
```
┌──────────┬──────────┬──────────┐
│  [FACE]  │ 📅 Next  │ 🍅 Pomo  │
│  (◕‿◕)  │ 2PM Call │  18:42   │
├──────────┼──────────┴──────────┤
│ 📋 TODO  │ 🦀 Rust Tip         │
│ • Vercel │ Use `dbg!()` macro  │
│ • Auth   │ for quick debugging │
└──────────┴─────────────────────┘
```

**Option C: Single Focus + Swipe**
Show one thing at a time, swipe/button to cycle.
Good for smaller screens.

---

## Tech Stack Options

### Frontend/UI

| Option | Pros | Cons |
|--------|------|------|
| **Web (React/Vue + local server)** | Familiar, easy animations | Heavier, needs browser |
| **Python + Pygame** | Lightweight, direct framebuffer | Less pretty |
| **Rust + egui/iced** | Learning opportunity! Fast | Steeper curve |
| **Flutter** | Beautiful UI, Pi support | Heavy runtime |
| **LVGL (C)** | Extremely lightweight | Complex |

**Recommendation:** 
- Quick MVP: Web app (Vite + React) running in Chromium kiosk mode
- Learning opportunity: Rust + `iced` (would be cool to build in the language you're learning)

### Communication

**How do I talk to the Pi?**

1. **File-based (simple)**
   - I write status to `~/.applekid/status.json` via Tailscale
   - Pi polls every few seconds
   - Low complexity, works offline

2. **WebSocket (real-time)**
   - Pi runs a small WS server
   - I push updates immediately
   - Better for notifications

3. **MQTT (IoT standard)**
   - Pub/sub messaging
   - Good for multiple devices later
   - Overkill for now

**Recommendation:** Start with file-based, upgrade to WebSocket if latency matters.

---

## Implementation Phases

### Phase 1: Basic Display
- [ ] Set up Pi with screen
- [ ] Create simple web UI (face + status text)
- [ ] File-based status updates
- [ ] Tailscale connection verified

### Phase 2: Notifications
- [ ] Pending/completed task display
- [ ] I integrate status updates into my workflow
- [ ] Basic notification sound

### Phase 3: Pomodoro
- [ ] Wire up button (GPIO or USB)
- [ ] Timer UI
- [ ] Break notifications

### Phase 4: Calendar
- [ ] Calendar API integration (or gog-based)
- [ ] Upcoming events display

### Phase 5: Flashcards
- [ ] Rust content database
- [ ] Quiz mode UI
- [ ] Spaced repetition (optional)

### Phase 6: Polish
- [ ] Animations refined
- [ ] Sound effects
- [ ] Edge cases handled

---

## Open Questions

1. **Screen size preference?** Smaller (3.5-4") is more minimal, 7" fits more info
2. **E-ink or LCD?** E-ink is calmer/always-on, LCD allows animations
3. **Input device budget?** DIY macro pad vs. buying something
4. **Rust UI as learning project?** Or just get it working with web tech?
5. **Where does the Pi live?** Desk? Wall-mounted? Affects design

---

## Next Steps

1. Confirm hardware (screen + Pi model + input device)
2. Set up Tailscale access
3. Build MVP: face + status display
4. Iterate from there

---

*Research by Applekid — January 29, 2026*
