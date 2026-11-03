# AI Calling Agent

A phone agent that answers real calls, talks like a receptionist
(Hindi / English / Hinglish), and holds a full conversation — built
with **FastAPI + Twilio + Google Gemini**.

Call the number → the agent greets you → you speak → it understands,
replies in voice, and remembers the conversation until you hang up.

## How it works

```
Caller ──► Twilio number ──► POST /voice   (FastAPI server)
                                 │  replies with TwiML:
                                 │  <Say> greeting + <Gather speech>
Caller speaks ──► POST /process (SpeechResult = caller's words as text)
                                 │
                                 ▼
                          Gemini LLM (+ conversation history)
                                 │  reply text
                                 ▼
                          <Say> speaks it, <Gather> listens again ──► loop
```

Design choice for v1: Twilio's built-in speech recognition (`<Gather>`)
and voice (`<Say>`) are used instead of separate STT/TTS services —
fewer API keys, less code, easy to explain and debug.

## Run it yourself

1. **Install**
   ```bash
   python -m venv venv
   venv\Scripts\activate        # Windows (source venv/bin/activate on Linux/Mac)
   pip install -r requirements.txt
   ```
2. **Add your Gemini key** — get a free one from Google AI Studio,
   copy `.env.example` to `.env` and paste it there.
3. **Start the server**
   ```bash
   uvicorn app:app --reload --port 8000
   ```
4. **Expose it to the internet** (Twilio must reach your laptop)
   ```bash
   ngrok http 8000
   ```
5. **Connect Twilio** — free trial account at twilio.com, get a trial
   phone number, and set its Voice webhook to
   `https://<your-ngrok-url>/voice` (POST).
6. **Call the number and talk to your agent.** 🎉

## Change the personality

Edit `SYSTEM_PROMPT` in `app.py` — that single prompt turns the same
code into a receptionist for a cafe, clinic, salon, coaching centre…

## Build roadmap (what I am adding next)

- [x] v1 — real phone calls, voice conversation loop, per-call memory
- [ ] v2 — take a table booking and save it (tool calling + SQLite)
- [ ] v2 — call summary saved after hangup
- [ ] v3 — streaming audio (Twilio Media Streams + Deepgram) for
      lower latency and a realistic human voice (ElevenLabs)

## Tech stack

Python · FastAPI · Twilio Voice (TwiML, speech Gather) · Google Gemini ·
ngrok (local tunnelling)
