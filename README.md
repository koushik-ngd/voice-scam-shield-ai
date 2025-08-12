# voice-scam-shield-ai

Link to the project files on Google Drive: https://drive.google.com/file/d/17ArVrmt8BZulkk9VnW0VeazoqW4e23Sh/view?usp=drive_link

Link to the project working screenshots on Google Drive: https://drive.google.com/drive/folders/1EfiH0_3sjc36UlENB6pQ_ALBnKrSbNMn?usp=sharing

About the project
AI Voice Scam Shield protects people during calls by evaluating short snippets of audio and telling them, in plain language and clear colors, whether the conversation appears safe, needs caution, or is likely a scam. The intent is calm guidance, not alarm—quick signals and simple choices so decisions are easier in the moment.

Architecture
The React frontend captures or simulates 3–5 second audio chunks, base64‑encodes them, and sends them to a FastAPI backend while showing live status and brief explanations. The backend manages stateless REST endpoints and optional WebSockets, holds live session state in Redis, and calls the AI layer for analysis. The AI layer uses ElevenLabs to score each chunk with a fraud likelihood and optional confidence plus explanation factors; when keys or quotas aren’t available, a deterministic mock returns stable, predictable results. Storage remains minimal by default: Redis for live state, optional PostgreSQL for history, and optional S3/GCS for recordings when explicitly enabled.

Data flow
A session starts and returns a session_id. The client streams base64 audio chunks to the backend, which validates the session, decodes audio, and posts it to the AI provider. The result is a score on a 0–1 scale that the backend maps to a label using sensitivity‑aware thresholds with optional smoothing and hysteresis to avoid flicker. The latest label and score are written to Redis and surfaced to the UI via a status endpoint or a WebSocket stream. When the call ends, a concise summary is ready for history, and the user can block, report, or mark safe.

Technical details
Audio is PCM WAV, mono, 16–24 kHz, chunked at 3–5 seconds and sent as base64 in JSON. Core endpoints cover creating a session, analyzing a chunk, reading current status, and managing user settings; a WebSocket endpoint can push live updates. Default thresholds classify Safe below 0.3, Suspicious between 0.3 and 0.7, and Scam above 0.7, with a user slider mapping sensitivity levels 1–5 to stricter or looser cutoffs. Reliability includes upstream timeouts mapped to a clear 502 “analysis_unavailable,” per‑session request limits to protect quotas, and a mock mode for development. Security keeps provider keys on the server, enforces HTTPS/WSS, disables recording by default, and minimizes stored data to the essentials.

Make your own version
Keep the backend response contract stable so any AI provider can be swapped without touching the frontend. Start with mock mode to build UX and flows, then enable live AI when ready. Begin with status polling for simplicity and add WebSockets for instant updates later. Tune sensitivity to your audience and enable recordings only when you have retention and access policies in place. Telephony integrations can feed media into the same chunking path.

Collaborators and roles:

Muaaz led the backend, delivering FastAPI services, Redis orchestration, deployment, and reliability.
Koushik led the AI work, integrating ElevenLabs, defining risk scoring, and shaping thresholds and smoothing. 
Aayan led the frontend, building the React UI for real‑time status, audio handling, dashboards, and settings.

Closing note:

The result is a quiet, fast, and privacy‑minded shield that supports good decisions during uncertain calls, built with care and meant to be extended.
