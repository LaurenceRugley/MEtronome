# MEtronome — Claude Project Memory

> Read this file at the start of every session. It contains everything needed to work on this codebase without losing context.

---

## Project Identity

- **App name:** MEtronome
- **Owner:** Laurence Alexander Rugley
- **Company:** LGR Capital Investments LLC
- **Target:** iOS 16+ · iPhone · App Store
- **GitHub repo:** github.com/LaurenceRugley — repo: MEtronome
- **Status:** App Store ready · Apple Developer Program enrolled · TestFlight in preparation
- **Trademark:** USPTO TEAS Plus filing in progress (Class 9) — ~$250
- **Monetisation:** Free + $2.99 Pro IAP (StoreKit 2) — in development

---

## Tech Stack

- Swift 5.9+ / SwiftUI (iOS 16+)
- AVAudioEngine + DispatchSourceTimer
- MultipeerConnectivity (Bluetooth sync)
- UserDefaults / @AppStorage · SwiftData planned (iOS 17+)
- Swift Charts · StoreKit 2 · WidgetKit (in dev)
- Zero external dependencies

---

## File Structure (33 Swift files · 4,124+ lines)

AppEntry.swift           @main root injector
MetronomeEngine.swift    AVAudioEngine + timer — core audio
SessionManager.swift     MultipeerConnectivity — Bluetooth sync
PracticeTracker.swift    Session logging, duration, streak
GoalsManager.swift       Practice goals + progress
AppSettings.swift        @AppStorage persistence
ContentView.swift        Root SwiftUI view
TimeSigSheet.swift       @Binding sheet — time sig picker
SettingsView.swift       Settings screen
METheme.swift            Design system — amber palette
BeatDotsView.swift       Animated beat dots
MEtronomWidget/          Separate Xcode target (WidgetKit)
install_icons.py         AppIcon installer — Terminal ONLY
MEtronome_AuditFixes.md  30 bugs fixed log

---

## Architecture

5 ObservableObjects owned by AppEntry as @StateObject:
engine / session / tracker / goals / settings
Injected via .environmentObject() into ContentView.
Child views use @EnvironmentObject — never pass as init params.

---

## Audio Engine Rules

- DispatchQueue label: "metronome.timer" qos: .userInteractive
- NEVER schedule audio on main thread
- NEVER downgrade QoS from .userInteractive
- 4 PCM buffers built at init (accentBeat, normalBeat, subdivision, silence)
- Timer interval: 60.0 / bpm seconds
- BPM changes apply at bar boundaries only
- ALWAYS handle AVAudioSession.interruptionNotification
- On .ended: try? audioEngine.start()
- Call WidgetCenter.shared.reloadAllTimelines() after BPM changes

---

## Design System — METheme.swift

Color.meDarkBG     = #161008  dark background
Color.meAmber      = #C07830  dark accent (brand amber)
Color.meLightBG    = #FDF6EC  light background
Color.meLightAmber = #A05C18  light accent

NEVER use Theme.accent — deleted. Always use Color.meAmber.

Fonts (must register in Info.plist UIAppFonts):
BebasNeue-Regular    → display / headers
OverpassMono-Light   → BPM numbers
OverpassMono-Regular → body mono

---

## CRITICAL — Xcode AI Rules

NEVER embed base64 in Xcode AI files. Causes credit exhaustion instantly.
Run python3 install_icons.py in Terminal ONLY — never Xcode AI.
Run after every clean build to reinstall AppIcons.

---

## Features Status

COMPLETE: AVAudioEngine PCM synthesis, tap tempo, time sig, loop mode,
BAR counter, rehearsal markers, pendulum toggle, Bluetooth sync,
practice tracker, goals, Swift Charts stats, METheme, AppIcon, App Store metadata

IN DEV: StoreKit 2 Pro IAP $2.99 · WidgetKit lock screen

PLANNED (priority order):
1. WidgetKit — first mover, screenshot differentiator (3-4 days)
2. Swing Quantisation — upbeat delay ratio 0.5-0.75 (2-3 days)
3. Gap Click — audio off / visual on per bar (1-2 days)
4. Tempo Trainer — BPM auto-increment at bar boundary (2-3 days)

---

## WidgetKit (in development)

App Group: group.com.lgrcapital.metronome
Keys: widget_bpm (Int), widget_timeSig (String)
Main app writes to App Group UserDefaults on every BPM/timeSig change.
Widget reads from App Group UserDefaults in TimelineProvider.
Families: .accessoryCircular (BPM) · .accessoryRectangular (BPM + timeSig)
Widget target is SEPARATE — cannot access EnvironmentObjects.
Copy METheme colors directly into widget target.

---

## Property Wrappers

@State           Local view value. Dies with view.
@Binding         Two-way ref to parent @State. No ownership.
@StateObject     Owns ObservableObject. Created once.
@ObservedObject  References ObservableObject it does not own.
@EnvironmentObject Injected dep. All descendants get it.
@AppStorage      UserDefaults-backed.
@Query           SwiftData fetch (iOS 17+).

---

## Bug Log — Fixed in v5 (30 total)

1. Engine silent after call/alarm → restart on AVAudioSession .interruptionNotification .ended
2. TimeSigSheet direct @EnvironmentObject mutation → @Binding var timeSig: String
3. Playlist sheet same mutation bug → same @Binding fix
4. SettingsView using deleted Theme.accent → Color.meAmber / Color.meLightAmber

---

## Git

Repo: github.com/LaurenceRugley/MEtronome · Branch: main
Auth: Personal Access Token in Terminal ONLY — never in chat
Never commit: base64, API keys, tokens, .env files

---

## Related Projects

UtilityLib C++      LaurenceRugley/UtilityLib       Live v1.1
CourtVision iOS+Web LaurenceRugley/court-vision      Ready to push
Crypto Monitor      LaurenceRugley/crypto-monitor    Live
Brand Studio        laurencerugley.github.io/MEtronome-Brand Live

---

## Developer

Laurence Rugley · Pasadena CA · lrugley@gmail.com
LGR Capital Investments LLC
4 yrs legal assistant eDiscovery (Ellis Law Firm)
Mt. SAC: CS, Calc 1+2, music theory, 6 yrs ensemble (Jazz Band, Wind Ensemble, Chamber Winds)
Rebel ShakeDown co-founder · keyboard + organ · Shwayze collab
Stack: Swift, Python, JavaScript, HTML/CSS, C++
Wife Savannah · Daughter due August 10 2026

---

## Session Startup Checklist

- [ ] Read CLAUDE.md (this file)
- [ ] Xcode AI credits topped up
- [ ] Load MEtronome_XcodeAI_Handover_v5.zip (33 Swift files)
- [ ] Confirm next feature (priority: WidgetKit)
- [ ] Pending Terminal: python3 install_icons.py
- [ ] Git push status (repo: MEtronome, branch: main)
