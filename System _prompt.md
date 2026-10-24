Build "TradeForge AI", a chat-based AI developer assistant that specializes in writing, reviewing, and debugging trading bots (Python and TypeScript, crypto and stock APIs, backtesting, risk management). It also accepts photos, videos, live camera, and screen sharing as input.

API SETUP (IMPORTANT)
- All AI calls go through a Supabase Edge Function named "ai-router". Never call the AI API from the frontend.
- The API is OpenAI-compatible at:https://api.xkiro.com/v1
- Read the key with Deno.env.get('XKIRO_API_KEY'). Prompt me to add this secret (name: XKIRO_API_KEY). Never hardcode it, log it, or expose it to the browser.
- Support streaming (SSE) responses, with proper error handling for 401, 429, and 5xx, plus a retry with backoff.

MODELS
- Planner: anthropic/claude-opus-5.5
- Coder: anthropic/claude-sonnet-5.5
- Reviewer: openai/gpt-6.1-sol
- Fast: anthropic/claude-haiku-5.5
Store model IDs in one config file so they are easy to change. Mark which models support vision in that config.

CORE FEATURES
1. ChatGPT-style chat UI: streaming, markdown, syntax-highlighted code blocks with a copy button, and saved conversation history per user (Supabase auth + database with row-level security).
2. Pipeline for coding requests: Planner makes a step-by-step plan, then Coder writes the code, then Reviewer audits it (bugs, security, look-ahead bias, missing stop-loss, API key leaks, rate limits). Show the final code with a short review summary. Simple questions go straight to Fast.
3. Mode selector: Auto, Plan, Code, Review, Quick. Show which model answered each message.
4. Project workspace: save generated files, file tree viewer, download as zip.
5. Trading templates: grid bot, DCA bot, EMA/RSI strategy bot, and a backtester with metrics (win rate, max drawdown, Sharpe ratio).
6. Usage tracker: tokens and estimated cost per model.
7. Settings page: pick the default mode and view model config. Do NOT ask users to enter the API key in the UI.

MEDIA & LIVE INPUT FEATURES
8. Attach button (paperclip) in the chat input with options: Photo, Video, Live Camera, Share Screen.
9. Photos: upload from the gallery or take a new one. Show a thumbnail preview before sending, allow multiple images, and compress client-side (max 1600px) before upload.
10. Videos: upload a video file (mp4/mov/webm). In the browser, extract a frame every 2 seconds (max 20 frames) using a canvas and send the frames as images with my message. Show a video preview and a progress bar while extracting.
11. Live Camera: use navigator.mediaDevices.getUserMedia with a live preview, front/back switch, a "Capture photo" button, and a "Stream mode" that sends a frame every few seconds while I chat. Add a clear Stop button.
12. Screen Share: use navigator.mediaDevices.getDisplayMedia with a live preview, a "Capture screenshot" button, and a "Stream mode" (a frame every 3-5 seconds). Keep the Stop button always visible. Show a fallback message if the browser doesn't support it.
13. Send all images and frames to the models in OpenAI-compatible format: {"type":"image_url","image_url":{"url":"data:image/jpeg;base64,..."}}.
14. Vision routing: when a message has media, send it only to vision-capable models. If the selected model lacks vision, switch to the Coder or Planner automatically and tell me.
15. Privacy: always show a clear "camera/screen is active" indicator, ask for permission each time, never record or store frames unless I tap "Save", and stop all streams when I leave the page.
16. Limits: cap images per message, total payload size, and stream duration to control token cost. Show an estimated cost before sending large media.
17. Supported use cases: send a screenshot of a chart or error for analysis, share my screen while debugging bot code, and show a trading chart through the camera or screen for strategy ideas.

SYSTEM PROMPT FOR ALL MODELS
"You are a senior quant developer. Write clean, production-ready, well-commented code. Always include error handling, logging, rate-limit handling, and risk controls (position sizing, stop-loss, max daily loss). Default to paper trading and testnet. Load secrets from environment variables and never hardcode keys. When given images, screenshots, or video frames, describe what you see before analyzing. Explain assumptions, and remind the user that no strategy guarantees profit."

DESIGN
Dark, minimal, developer-focused theme. Responsive and mobile friendly, with large touch-friendly buttons for camera and screen controls.
