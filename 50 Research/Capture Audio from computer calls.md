
NOTE: I tink it messed up Tranlate to another languish instead converting it from audio to text. Need to redo this. 

## Existing Tools (Ranked by Value for Your Use Case)

### **Top Picks for Your Workflow**

**1. Übersetz** ($0) — Most flexible for a developer  
Captures system audio + microphone, runs Whisper locally on Apple Silicon, or opts into GPT Realtime/Gemini Live on-demand. Saves transcripts, supports 100+ languages, and integrates with your own API endpoints. Perfect for someone who wants control without upfront costs. [https://ubersetz.app](https://ubersetz.app)

**2. EchoMeet** ($0) — Purpose-built for calls  
Captures system audio + your mic (even Bluetooth headphone audio), transcribes via macOS on-device speech recognition (free), and translates with your own OpenAI API key. Exports directly to **Markdown with speaker labels and translations side-by-side**—exactly what you need for meeting notes. Runs entirely on-device. [https://echomeet.xyz/)](https://echomeet.xyz/)

**3. Echosy** (Free/€49 one-time) — Privacy-first transcription  
Lives locally on your Mac, captures system + mic in real-time, translates locally, and integrates with your own OpenAI/Gemini/Ollama endpoints for summaries. No cloud upload during capture. [https://echosy.org/](https://echosy.org/)

**4. TranscribeX** (Free/paid) — Auto-detection + meeting minutes  
Detects when a meeting starts (Teams, Zoom, Google Meet, Slack, Discord), auto-records audio + video, transcribes and diarizes (speaker ID), and generates AI summaries and action items—all locally. Exports as Markdown. [https://www.transcribex.io/](https://www.transcribex.io/)

**5. AirTranslate** ($0, open source) — Pure Apple Silicon  
Uses macOS Sequoia's built-in Speech Recognition and Translation frameworks (no API keys needed). Captures system audio via ScreenCaptureKit, optional GPT Realtime if you provide your own key. Open source on GitHub. [](https://himomohi.github.io/AirTranslate/)

---

## Building Your Own (Developer Path)

You can absolutely build this. The stack is:

### **Audio Capture**

- **System audio**: Use `ScreenCaptureKit` (macOS 12.3+) — no virtual drivers, one API call, delivers `CMSampleBuffer`objects
- **Microphone**: Use `AVAudioEngine` with a tap on the input node — receives live buffers in real-time
- Both run simultaneously; you combine them at the buffer level

### **Transcription**

- **Local**: `whisper.cpp` (runs on Metal GPU, ~50–70ms latency for medium model on M1/M2)
- **API**: OpenAI Whisper API ($0.006/minute) — use when you transcribe <10 hrs/day; break-even vs. self-hosted at ~250+ transcription hours/month
- Language: Python (`faster-whisper`), Node.js (`@kutalia/whisper-node-addon`), or call the CLI

### **Translation**

- **Local**: macOS Translation framework (Sequoia+) — built-in, free, any language pair
- **API**: DeepL ($5/mo starter) or OpenAI ($0.01–0.015/1K tokens) — route translated text only, not raw audio

### **Markdown Output & Integration**

- Capture the transcript stream, store in SQLite, export as Markdown with speaker labels
- Reference your Obsidian vault: open markdown files in a floating window during calls (use NSPanel or SwiftUI overlay)
- Prompt to save: use `NSSavePanel` or auto-save to a `~/Transcripts/` folder with timestamp

### **Cost Reality**

|Approach|Setup|Per-minute cost|Privacy|
|---|---|---|---|
|Local whisper.cpp + macOS Translation|30 min|$0|100% on-device|
|Local whisper.cpp + DeepL API|30 min|$0.0001–0.0002 (amortized) + ~$0.0003 (translation)|Audio local, text sent to DeepL|
|OpenAI Whisper API + DeepL|5 min|$0.006 + $0.0003|Audio sent to OpenAI|
|Übersetz (hybrid: local Whisper + optional APIs)|2 min (download)|$0–0.006|Your choice|

---

## Recommendation

**Start with Übersetz or EchoMeet** (both free, both local-first). They let you:

- Test the workflow without building
- See which features matter most
- Drop in your own OpenAI keys only for features you want

**Build your own if:**

- You need tighter Obsidian integration (live sidebar with your notes during calls)
- You want to own the entire pipeline (no third-party data flow)
- You plan to transcribe >100 hours/month (economics favor local Whisper)

**Quick DIY Stack** (Node.js + Swift):

1. Swift CLI tool using `ScreenCaptureKit` + `AVAudioEngine` → raw PCM to stdout
2. Node.js process pipes that into `whisper.cpp` CLI → reads transcripts line-by-line
3. Optional: pipe translations through local Translation framework or DeepL API
4. Save to SQLite, export Markdown with timestamps + speaker labels
5. Electron wrapper or SwiftUI menu bar app to open Obsidian notes in a side panel

The hard part isn't the tech—it's handling device switching mid-call (AirPods to speakers) and overlapped speech diarization. Both existing tools handle this; if you DIY, use `SpeakerKit` for speaker separation on the system audio lane.