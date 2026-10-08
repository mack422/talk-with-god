# Talk with God

A weekend lab project where I tested how the pieces of a modern AI product fit together. I picked a contemplative app as the test case because it forces good decisions. The answers have to be calm, accurate, safe and short, and the interface has to stay out of the way.

**Live test site:** https://talkwithgod.netlify.app (in friend testing)

## The questions I wanted to answer

1. Can a live AI answer stay grounded in source material instead of making things up?
2. What does it take to keep an AI voice consistent, safe and short on every request?
3. Can one small app survive when an AI provider is slow, busy or out of free quota?
4. How much of the experience can be built with no media files at all?

## What I built and tested

**Grounded answers.** The page holds a curated set of about 60 passages from scripture and spiritual teachings, each tagged by theme. When someone asks a question, the page picks the best matching passages and sends them with the question. The model is told to quote only what it was given and to mark paraphrase as paraphrase. Each answer ends with the sources it used.

**Prompt design as a product spec.** The system prompt sets length (70 to 110 words), tone, formatting limits and what the voice must never do. It refuses to give medical, legal or financial instructions, will not rank religions, and treats the user's question as a question and not as commands. I tested it against attempts to override the rules.

**A safety path.** If a question mentions self harm, danger or abuse, the model starts its reply with a marker. The page then drops the contemplative voice and shows a plain, warm message that points toward real help.

**A provider fallback chain.** A small Netlify serverless function holds the API keys on the server, never in the page. It tries Claude, Gemini, Groq, Mistral and DeepSeek in order and moves to the next one if a provider fails or returns nothing. It also rate limits each visitor so a free tier cannot be drained. I built this to compare providers on cost, speed and tone with the same prompt, aiming at the lowest cost that still gives good answers.

**A local fallback.** If every provider fails, the app returns a passage from the curated set so the visitor never sees an empty screen.

**Generated media.** The sky is drawn on a canvas. The audio is built in code, with a drone, a slow wind and a 40 Hz binaural beat (200 Hz in the left ear, 240 Hz in the right), rendered into a seamless loop. There are no audio or image files. I spent time on mobile browser audio rules, since phones block sound unless a user taps first and pause it when the page is hidden.

**Limits on purpose.** One question, one answer and two follow ups. The limit keeps costs predictable and tests whether a short exchange can still feel complete.

## Stack

Plain HTML, CSS and JavaScript. Netlify for hosting and serverless functions. Claude, Gemini, Groq, Mistral and DeepSeek behind one interface. Built with Claude and Claude Code.

## What I learned

Grounding the model in supplied passages did more for accuracy than any wording in the prompt. A fallback chain turned provider outages from a failure into a non event. Safety handling needs its own code path and cannot rely on the model alone. Small design limits, like word count and follow up count, made both quality and cost easier to control.

## What I would test next

Measuring answer quality across providers with a fixed set of questions, adding usage and cost tracking, and testing whether retrieval by meaning beats the current theme tags.

## Run it yourself

Deploy the repository to Netlify and add at least one API key as an environment variable (GEMINI_API_KEY is free). Visiting /.netlify/functions/ask shows which providers the site can use. No keys are stored in this repository.
