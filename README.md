<div align="center">

<br/>

```
  ┌─────────────────────────────────────┐
  │                                     │
  │      ⏱  S C R O L L G U A R D      │
  │                                     │
  └─────────────────────────────────────┘
```

**Stop the scroll spiral. Reclaim your focus.**

A self-contained, zero-dependency browser app that monitors your Instagram and YouTube usage in real time — interrupting your sessions with in-app overlays, sound alerts, and native OS notifications even after you've switched tabs.

<br/>

[![License: MIT](https://img.shields.io/badge/License-MIT-0e0e12?style=flat-square&labelColor=f5f4f0)](LICENSE)
[![HTML5](https://img.shields.io/badge/HTML5-Single%20File-E34F26?style=flat-square&logo=html5&logoColor=white)](scrollguard-v4.html)
[![No Dependencies](https://img.shields.io/badge/Dependencies-Zero-1db954?style=flat-square)](scrollguard-v4.html)
[![Service Worker](https://img.shields.io/badge/Service%20Worker-Enabled-6366f1?style=flat-square)](scrollguard-v4.html)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-bc1888?style=flat-square)](CONTRIBUTING.md)

<br/>

![ScrollGuard Auth Screen — dark editorial interface with animated gradient orbs](https://placehold.co/860x480/0e0e12/f5f4f0?text=ScrollGuard+%E2%80%94+Auth+Screen&font=playfair-display)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [How It Works](#how-it-works)
- [Getting Started](#getting-started)
- [Usage Guide](#usage-guide)
- [Notification System](#notification-system)
- [Architecture](#architecture)
- [Browser Support](#browser-support)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

ScrollGuard is a **single-file, client-side web application** built to help you set intentional time limits on social media usage. It runs entirely in the browser — no server, no backend, no tracking — and uses the **Web Notifications API** + an **inline Service Worker** to fire device-level alerts even when you've navigated away from the ScrollGuard tab and are actively scrolling Instagram or YouTube.

The app is opinionated by design. You connect your platforms, set a session limit, start the timer, and ScrollGuard handles the rest — interrupting you at the halfway point, again at 5 minutes remaining, and with a final dismissible overlay when time is up.

> **Why a single HTML file?**  
> Zero setup friction. Open it, use it. No `npm install`, no bundler, no deployment pipeline. The entire application — styles, logic, Service Worker, and UI — ships in one portable file you can save to your desktop and open directly.

---

## Features

### 🔐 Authentication
- Sign In / Create Account flow with **real-time email validation** and password strength meter
- User session persists in-memory for the lifetime of the tab
- Clean sign-out that fully resets application state

### 🔗 Social Account Linking
- Connect **Instagram** and **YouTube** accounts via email verification
- Each platform uses the **same email as your ScrollGuard login** for instant one-click verification
- Accounts can be unlinked at any time from the My Apps screen
- Per-app individual time limits configurable independently

### ⏱ Countdown Timer
- Animated SVG ring countdown — colour shifts from **purple → amber → red** as time depletes
- Adjustable session duration: **5 to 120 minutes** in 5-minute increments
- Live platform tracking pills show which apps are being monitored in real time
- Pause and Resume support mid-session
- Urgent pulse animation in the final 60 seconds

### 🔔 Three-Layer Notification System

| Trigger | In-App Overlay | Sound Alert | Background Push |
|---|---|---|---|
| Session start | — | Single soft beep | ✅ Confirmation push |
| 50% remaining | ✅ Mindfulness card | 2-tone ascending chime | ✅ Check-in push |
| 5 min remaining | ✅ Warning card | Triple urgent beep | ✅ Persistent push (stays until tapped) |
| Session end | ✅ Completion card | 3-note triumphant chime | ✅ Persistent push (stays until tapped) |

### 📱 Background Notifications (tab-switch aware)
- Registers an **inline Service Worker** (no external file required) that survives tab switches
- Native OS-level notifications delivered via `showNotification()` — appear in the device's system notification tray
- `visibilitychange` listener catches up the timer accurately after the tab is restored, correcting for any hidden time
- Session chip badge visible in corner when tab is in background

### 📊 Statistics & Gamification
- Tracks total sessions, total time monitored, and current daily streak
- **8 unlockable achievements**: First Session, 3-Day Streak, IG Warrior, YT Guardian, 5 Sessions, 1 Hour Reclaimed, Mindful Week, 10 Sessions
- Session history log with timestamps and platform labels
- Profile screen with lifetime stats

### ⚙️ Settings
- Toggle 5-minute warning, halfway check-in, and auto-restart independently
- Enable / disable background notifications with a single tap
- Notification status indicator shows current permission state (granted / denied / pending)

---

## How It Works

```
┌──────────────────────────────────────────────────────────────────┐
│                         USER FLOW                                │
│                                                                  │
│  Sign In/Up ──► Connect Apps ──► Set Limit ──► Start Session    │
│                    (email           (5–120                       │
│                  verification)       min)                        │
│                                        │                         │
│                                        ▼                         │
│                               Timer Running                      │
│                              (setInterval)                       │
│                                        │                         │
│                    ┌───────────────────┼───────────────────┐    │
│                    ▼                   ▼                   ▼    │
│              At 50% left         5 min left           Time = 0  │
│            Halfway overlay    Warning overlay     Done overlay   │
│            + Sound chime      + Sound chime     + Sound chime   │
│            + SW push notif    + SW push notif   + SW push notif │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

### Background Notification Architecture

```
┌────────────────┐      postMessage()      ┌──────────────────────┐
│  Main Page JS  │ ──────────────────────► │   Service Worker     │
│                │                         │  (Blob URL, inline)  │
│  triggerWarn() │                         │                       │
│  triggerDone() │                         │  showNotification()  │
│  triggerHalf() │                         │  ──────────────────► │
└────────────────┘                         │    OS Notification   │
                                           │    Tray / Banner     │
        ▲                                  └──────────────────────┘
        │
   visibilitychange
   catches tab-return,
   syncs timer to wall clock
```

The Service Worker is constructed at runtime as a `Blob` URL — meaning no external `.js` file is needed. This lets the app work when opened directly from the filesystem (`file://`) or served from any origin.

---

## Getting Started

### Option 1 — Open directly (simplest)

```bash
# Clone the repo
git clone https://github.com/your-username/scrollguard.git

# Open in browser — no server needed
open scrollguard-v4.html
# or on Windows:
start scrollguard-v4.html
```

> **Note:** Background notifications via Service Worker require the page to be served over `http://` or `https://`. When opened as `file://`, the in-app overlays and sound alerts still work fully; only the OS-level push notifications are restricted.

### Option 2 — Local server (full features including SW push)

```bash
# Python 3
python -m http.server 8080

# Node.js (npx)
npx serve .

# Then open:
# http://localhost:8080/scrollguard-v4.html
```

### Option 3 — GitHub Pages / Netlify / Vercel

Drop the single HTML file into any static hosting service. No build step required.

```bash
# Example: deploy to Netlify via CLI
netlify deploy --prod --dir . --message "ScrollGuard v4"
```

---

## Usage Guide

### 1. Create your account

Enter any valid email address and a password of 6+ characters. This is a **demo auth system** — credentials are stored in-memory only and reset when the tab is closed. No data is sent anywhere.

### 2. Connect Instagram and/or YouTube

Tap either platform card. In the bottom sheet that slides up, enter the email address you use for that social media account. If it matches your ScrollGuard login email, you'll see an instant green ✅ verification badge. Mismatches show a yellow warning — you can still connect, it just won't be auto-verified.

### 3. Set your session limit

Use the slider on the Timer screen to set how many minutes you want to allow yourself (5–120 min). The default is **30 minutes**.

### 4. Select platforms and start

Tap the platform pills to choose which apps to track this session, then press **▶ Start Session**. You'll see the ring begin counting down and a live indicator chip appear.

### 5. Switch to Instagram or YouTube

ScrollGuard keeps the timer running in the background. When key moments arrive, you'll receive:
- A native OS notification on your device (if permission was granted)
- An in-app overlay waiting for you when you return to the tab
- Sound alerts playing through your device speaker

### 6. Respond to alerts

**Halfway card** — tap "Keep Going" to acknowledge and continue.  
**5-min warning** — tap "Got It" to dismiss.  
**Session complete** — tap "I'll Stop Scrolling" to end cleanly, or "5 More Minutes (Last Resort)" for a single one-time extension.

---

## Notification System

### Granting permission

The first time you start a session, a **permission banner** slides up from the bottom asking you to allow notifications. Tap **Allow** — the browser will show its native permission dialog.

Alternatively, go to **Settings → Notification Methods → Background notifications** and tap the toggle.

### Permission states

| State | Indicator | Behaviour |
|---|---|---|
| `granted` | 🟢 Green dot | Full OS notifications via Service Worker |
| `default` (not yet asked) | ⚫ Grey dot | Banner shown on session start |
| `denied` | 🔴 Red dot | In-app overlays + sound only; instructions shown to re-enable in browser settings |

### Re-enabling after blocking

If you accidentally denied notifications:

- **Chrome / Edge:** Click the 🔒 padlock in the address bar → Notifications → Allow → Reload
- **Firefox:** Click the shield icon → Permissions → Notifications → Allow
- **Safari:** Settings → Websites → Notifications → find the site → Allow

### What each notification looks like

```
┌─────────────────────────────────────────┐
│ ⚠️ ScrollGuard                  now     │
│ 5 Minutes Left!                         │
│ Instagram — time is almost up. Start    │
│ wrapping up now.                        │
└─────────────────────────────────────────┘
```

The 5-minute warning and session-complete notifications use `requireInteraction: true` — they stay pinned on screen until you explicitly tap to dismiss, ensuring you can't miss them while scrolling.

---

## Architecture

```
scrollguard-v4.html
│
├── <style>                    CSS design system
│   ├── Design tokens          CSS custom properties (colours, radii, gradients)
│   ├── Screen system          .screen / .screen.active display toggling
│   ├── Component styles       Cards, rings, overlays, toggles, nav, modals
│   └── Notification UI        Banner, status dot, background chip
│
├── <body>                     HTML screens
│   ├── #authScreen            Sign In / Create Account
│   ├── #onboardScreen         3-step guided setup
│   ├── #timerScreen           Main countdown timer (home tab)
│   ├── #appsScreen            Connected platforms + per-app limits
│   ├── #settingsScreen        Toggles + account
│   ├── #profileScreen         Stats, linked accounts, achievements
│   ├── #link-modal            Bottom sheet for account linking
│   ├── #ov-half               Halfway overlay
│   ├── #ov-warn               5-minute warning overlay
│   ├── #ov-done               Session complete overlay
│   ├── .notif-banner          Permission request banner
│   └── .bg-active-chip        "Session live" indicator when tab is hidden
│
└── <script>                   Application logic
    ├── SW_CODE (string)        Inline Service Worker source
    ├── registerSW()            Blob URL SW registration
    ├── swNotify()              postMessage → SW → showNotification()
    ├── sendBgNotif()           Wrapper: SW path + Notification API fallback
    ├── Auth functions          doLogin(), doSignup(), doLogout()
    ├── Onboarding              ob1Next(), ob2Next(), goApp()
    ├── Link modal              openLink(), confirmLink(), checkLinkEmail()
    ├── Timer engine            startTimer(), tick(), pauseTimer(), resetTimer()
    ├── Alert triggers          triggerHalf(), triggerWarn(), triggerDone()
    ├── Notification system     requestNotifPermission(), updateNotifStatusUI()
    ├── Visibility sync         visibilitychange → wall-clock catch-up
    ├── Stats & log             updateStats(), addLog(), clearLog()
    ├── Render helpers          renderTrackPills(), renderConnList(), etc.
    └── Navigation              showScreen(), navTo(), syncSettings()
```

### Key design decisions

**Inline Service Worker via Blob URL**  
Instead of requiring a separate `sw.js` file on the server, the SW source is stored as a template string and registered from a dynamically created `Blob`. This keeps the app truly self-contained and works from any origin, including `localhost` and served subdirectories.

**Wall-clock catch-up on tab restore**  
`setInterval` is throttled by browsers when a tab is hidden (typically to 1-second intervals at best, sometimes much slower). ScrollGuard records `Date.now()` when the tab is hidden and, on `visibilitychange` back to visible, computes exact elapsed seconds and subtracts them from the remaining time — ensuring the timer is always accurate regardless of how long the tab spent hidden.

**No external dependencies**  
All styling, animations, sound synthesis (Web Audio API), and logic are written from scratch. Google Fonts is the only external resource, and the app degrades gracefully without it.

---

## Browser Support

| Browser | In-App Overlays | Sound Alerts | Background SW Push |
|---|---|---|---|
| Chrome 88+ | ✅ | ✅ | ✅ |
| Edge 88+ | ✅ | ✅ | ✅ |
| Firefox 79+ | ✅ | ✅ | ✅ |
| Safari 16.4+ | ✅ | ✅ | ✅ (with permission) |
| Safari < 16.4 | ✅ | ✅ | ❌ SW push not supported |
| Chrome (Android) | ✅ | ✅ | ✅ |
| Safari (iOS 16.4+) | ✅ | ✅ | ✅ (Home Screen PWA only) |

> **iOS note:** Web push notifications on iOS require the page to be added to the Home Screen as a PWA. Safari does not support push from a regular browser tab on iOS.

---

## Roadmap

- [ ] **PWA manifest** — installable to home screen with offline support
- [ ] **IndexedDB persistence** — session history and stats survive tab close
- [ ] **TikTok & X/Twitter** — extend platform support beyond IG and YouTube
- [ ] **Weekly insights** — chart of daily usage patterns over 7 days
- [ ] **Scheduled sessions** — auto-start a timer at a configured time of day
- [ ] **Custom sound themes** — choose from different alert chime sets
- [ ] **Streak calendar** — GitHub-style heatmap of daily session completion
- [ ] **Export data** — download session history as CSV
- [ ] **Dark mode for main screens** — full dark theme toggle (auth screen is already dark)
- [ ] **Accessibility pass** — ARIA roles, keyboard navigation, reduced-motion support

---

## Contributing

Contributions are welcome. Please open an issue before submitting a pull request for significant changes so the direction can be discussed first.

```bash
# Fork & clone
git clone https://github.com/your-username/scrollguard.git
cd scrollguard

# Create a feature branch
git checkout -b feature/my-improvement

# Make changes to scrollguard-v4.html
# Test in browser (serve locally for full SW support)
python -m http.server 8080

# Commit with a descriptive message
git commit -m "feat: add weekly insights chart"

# Push and open a PR
git push origin feature/my-improvement
```

### Contribution guidelines

- Keep the app **single-file** — all changes go into the HTML file
- Do not introduce npm dependencies or build steps
- Test in Chrome and Firefox at minimum before submitting
- Follow the existing CSS variable and naming conventions
- Sound alert additions should use the Web Audio API (no audio files)

---

## License

MIT © 2025 — see [LICENSE](LICENSE) for full text.

---

<div align="center">

Built with focus, for focus.

**[⬆ Back to top](#)**

</div>
