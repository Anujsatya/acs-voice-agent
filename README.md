# ACS Voice Agent

An outbound voice AI agent built on Azure Communication Services and [Pipecat](https://github.com/pipecat-ai/pipecat). It places phone calls, has a real-time conversation using an STT → LLM → TTS pipeline, detects voicemail, and logs structured call results and transcripts.

## Pipeline

- **Telephony**: Azure Communication Services (Call Automation + SMS)
- **STT**: Deepgram
- **LLM**: Azure OpenAI / OpenAI (GPT-4o)
- **TTS**: ElevenLabs / Inworld
- **Orchestration**: Pipecat, with a concurrent dialer for batched outbound campaigns

## Features

- Concurrent outbound dialer with configurable batch size, concurrency cap, and inter-call delay
- Pre-recorded + live voicemail detection with a fallback voicemail message
- Per-call transcript logging and incremental result capture (so a crash mid-campaign doesn't lose completed calls)
- Goodbye-safety hangup fallback (ends the call cleanly if the model doesn't)
- Simple web UI (`app/static/`) for monitoring live calls and running the dialer

## Setup

```bash
pip install -r requirements.txt
cp .env.example .env   # fill in your own credentials and endpoints
```

Then populate `campaign_input.csv` with your own call list:

```csv
org_name,phone_number,services,unique_id
```

Run the server:

```bash
python -m app.main
```

## Project structure

```
app/
  main.py                 FastAPI app + entrypoints
  dialer.py / new_dialer.py / dialer_manager.py   Outbound batch dialer logic
  acs_transport.py        Azure Communication Services call transport
  pipecat_pipeline.py     STT/LLM/TTS pipeline wiring
  call_session.py         Per-call state machine and conversation flow
  call_timeline.py        Call event timeline tracking
  transcript_processor.py Transcript capture and formatting
  agent_settings.py       Agent configuration and system prompt assembly
  samantha_prompt.py      The agent's conversational system prompt
  ui_events.py            Server-sent events for the monitoring UI
  static/                 Web UI (dialer, index, monitor pages)
  assets/                 Pre-recorded voicemail audio
```

## Notes

This repo is a sanitized version of a working prototype — API keys, call transcripts/results, and campaign call-list data have all been stripped. The agent's identity/company name used in the system prompt has been genericized to "Acme Health" as a placeholder.

## License

MIT
