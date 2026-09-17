# Technical Note: Say It Back SPA

### Research Source & Learning Idea
This application is based on **retrieval practice with immediate corrective feedback**, drawing from Roediger & Karpicke (2006), “Test-Enhanced Learning: Taking Memory Tests Improves Long-Term Retention”, *Psychological Science* 17(3). 

**Mapping to the app:** Every multiplication fact must be produced—spoken or typed—by the learner before it is shown. A missed fact gets immediate corrective feedback, a spoken “say it back” rehearsal step, and a delayed re-test a few items later (rather than an instant second guess, which would only test recognition). 

**Failure condition:** If the app showed answers before the learner tried, saved feedback until the end of the round, or re-tested immediately, it would remove the effortful retrieval that drives the learning effect, rendering it no better than a static slide deck.

### What I Built
A voice-interactive Single Page Application (SPA) featuring "Abi," an animated mascot that guides the user through times-tables practice. The app runs entirely in the browser without a backend, using the Web Speech API for spoken interaction and the Web Audio API for real-time microphone visualizations. 

### How AI Tools Were Used
I used an AI assistant to accelerate development, by giving 3 prompts across three main iterations:

1. **Prompt 1 (Generation):** Drafted the initial SPA code, including the HTML structure, CSS styling for the notebook-paper aesthetic, and the core JavaScript logic for the retrieval practice loop.
2. **Prompt 2 (UI/UX Refinements & Bug Fixing):** Prompted the model to fix the incorrect answer display (changing the answer color from green to red when wrong, and removing the red circle that was obscuring the numbers). I also had the model fix a broken muting logic by restricting the mute/unmute toggle to only be active *before* a round starts.
3. **Prompt 3 (Troubleshooting & Design):** Prompted the model to investigate microphone detection issues and redesign the Abi mascot to look more child-friendly and visually pleasing.

**Self-included changes (Manual adjustments):**
1. **TTS Voice:** Changed the system voice selection logic and model name in the code to route to a more natural, human-sounding premium system voice.
2. **Response Variety:** Wrote and integrated custom response arrays for different correct/incorrect states so the mascot sounds less robotic and repetitive.
3. **Microphone Fix:** Discovered the ASR "unreachable speech service" error was a Brave-browser-specific privacy shield issue, not a code bug. Resolved by testing/running in a standard Google Chrome Browser.

### One AI Suggestion I Rejected 
**Suggestion:** When the mute button was buggy (unmuting mid-speech didn't immediately resume audio), the AI model suggested rewriting the complex dynamic audio queue logic so TTS could seamlessly resume from the exact moment it was muted.
**Why I rejected it:** Dynamically pausing and resuming the browser's `speechSynthesis` queue is notoriously buggy across different browsers. Instead, I chose the simpler, more robust UX solution: disabling and removing the mute/unmute button once a round starts. This prevented race conditions in the audio queue without requiring complex, brittle workarounds.

### Interaction Path & ASR/TTS Wiring
1. **Setup:** The user selects a times table and clicks "Start". `window.speechSynthesis` (TTS) introduces the round.
2. **Ask:** Abi speaks the question (e.g., "What is seven times eight?"). The `onboundary` event of the SpeechSynthesisUtterance is wired to the UI to reveal text in the speech bubble word-by-word, synced with the audio.
3. **Listen (ASR):** Once TTS ends, the app triggers `window.SpeechRecognition` (ASR) and opens a mic stream via `navigator.mediaDevices.getUserMedia`. The stream is piped to an `AnalyserNode` (Web Audio API) to draw a live waveform on a canvas while the user speaks. Interim ASR results are displayed on screen.
4. **Evaluate & Feedback:** The app parses the ASR transcript for numbers (handling variations like "fifty-six" or "5 6"). If correct, TTS praises the user and moves to the next item. If wrong, TTS gives the correct answer and prompts a "say it back" phase, queuing the missed item for a delayed retest.

### How I Would Improve this Application
Replace the browser's native Web Speech API with a cloud-based conversational voice API (like OpenAI's Realtime API or ElevenLabs). This would drastically reduce the robotic latency of TTS, improve ASR accuracy for child voices (which native browser ASR often struggles with), and allow for more fluid, natural conversational pacing.