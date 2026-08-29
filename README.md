# 🤖 Personal BOS (Brand OS)

> Autonomous AI system that manages, generates, and optimizes personal brand content daily — with brand voice consistency scoring, multi-platform publishing, and weekly strategic reports — running 24/7 with zero manual intervention.

---

## What This System Does

Every day at 06:30 AM WIB, this pipeline runs automatically:

1. **Loads** brand profile and voice rules from database
2. **Generates** daily content package aligned to brand pillars
3. **Scores** content with AI brand voice QA layer (0-100)
4. **Publishes** to Telegram — post, Twitter thread, LinkedIn post
5. **Logs** all data to Google Sheets for performance tracking
6. Every Sunday — generates and sends **Weekly Brand Report**

---

## System Architecture

[Google Sheets — Brand Profile Database]
↓
[load_brand_profile] — loads profile, voice rules, recent topics
↓
[generate_brand_content] — Groq `openai/gpt-oss-120b` creates daily content package
↓
[brand_voice_qa] — AI evaluates brand consistency score 0-100
↓
[publish_and_log] — publishes to Telegram + logs to Sheets
↓
[WF2 - Weekly Report] — every Sunday, strategic insights report

---

## Content Generated Daily

Every run produces a complete daily content package:

| Content Type | Description |
|---|---|
| Daily Theme | Strategic topic aligned to content pillars |
| Twitter Main | Standalone tweet with hook and value |
| Twitter Thread | 5-tweet thread with data, insight, and CTA |
| LinkedIn Post | 150-word professional post with line breaks |
| Telegram Post | Conversational post with emojis |
| AI Visual | Generated image matching the daily theme |
| Brand Score | AI quality score 0-100 for brand consistency |

---

## Brand Voice QA Layer

Every piece of content passes through an AI quality check before publishing:

- Evaluates against brand voice rules
- Checks always-do and never-do guidelines
- Returns brand consistency score 0-100
- Auto-approves if score above threshold
- Flags risky content before it goes live

---

## Weekly Brand Report

Every Sunday at 20:00 WIB, the system generates:

- Posts published vs target (X/7 days)
- Average brand voice score for the week
- Week grade (A+ to C)
- Consistency rating
- Momentum trend (RISING / STABLE / DECLINING)
- Key strategic insight
- Next week focus recommendation
- Content gap analysis

---

## Dashboard Features

- **Recent Published Posts feed** — latest 10 posts with theme, pillar tag, brand score, and tweet preview
- **Resilient image loading** — post visuals now show a loading skeleton while the image loads, and fall back to a clear "image unavailable" placeholder if the Pollinations.ai URL is slow, still generating, or unreachable, instead of the small broken-image icon it used to show
- **Weekly Brand Reports** — grade, momentum trend, insight, and next-week focus for the last 4 weeks
- **Auto-refresh** every 5 minutes

---

## Tech Stack

| Category | Tools |
|---|---|
| Workflow Automation | Pipedream |
| AI Content Generation | Groq AI (`openai/gpt-oss-120b`) |
| Brand Voice QA | Groq AI (`openai/gpt-oss-120b`) |
| Image Generation | Pollinations.ai |
| Database | Google Sheets API |
| Publishing | Telegram Bot API |
| Auth | Google Service Account, JWT |

**Cost: $0/month** — 100% free tier infrastructure

> **Model update:** Groq deprecated `llama-3.3-70b-versatile` and `llama-3.1-8b-instant` on the free/developer tier. Both content generation and the brand voice QA layer now run on **`openai/gpt-oss-120b`** — Groq's officially recommended replacement, still fully free-tier, and it benchmarks above the old Llama 3.3 70B on reasoning and instruction-following while running faster on Groq's LPU hardware. If you ever need lighter rate limits, `openai/gpt-oss-20b` or `qwen/qwen3.6-27b` are the other free options Groq points to, but 120B is the strongest of the three and stays the default here.

---

## Brand Profile Configuration

The system is fully configurable via Google Sheets:

brand_profile sheet:

- name
- title
- niche
- target_audience
- brand_voice
- core_values
- content_pillars
- posting_frequency
- platforms

brand_voice_rules sheet:

- always_do rules
- never_do rules
- tone_words

No code changes needed — update the sheet and the system adapts automatically.

---

## Sample Output

**Daily Theme:** Why Most Developers Underestimate No-Code Tools

**Brand Score:** 92/100

**Twitter Thread:**

1/ Most developers dismiss no-code tools without trying them. Big mistake. Here is why 🧵
2/ No-code tools in 2026 can handle: webhooks, API calls, database ops, scheduled jobs, and AI integration.
3/ I built a recruitment pipeline that scores 100+ candidates automatically. Zero backend code. Runs 24/7.
4/ The rule: if a tool can do 80% of the job in 20% of the time, use it. Save code for what actually needs it.
5/ No-code is not the future. It is the present. Are you using it? #AI #NoCode #Automation #Developer

**LinkedIn Post:**
Most developers I talk to have never seriously tried no-code tools.

They assume it is just for non-technical people. They are wrong.

In the past 6 months I built production systems using Pipedream, Supabase, and Groq AI that handle real operational workloads — recruitment pipelines, customer service automation, content systems — all without writing a traditional backend.

The shift is not about replacing code. It is about knowing when not to write it.

What no-code tools are you using in your workflow?

---

## Workflows

**WF1 — Daily Content Generator**
- Trigger: Every day 06:30 AM Asia/Jakarta
- Steps: load_brand_profile → generate_brand_content → brand_voice_qa → publish_and_log

**WF2 — Weekly Brand Report**
- Trigger: Every Sunday 20:00 Asia/Jakarta
- Steps: generate_weekly_report

---

## Environment Variables

GROQ_API_KEY                = gsk_xxxx
GROQ_MODEL                  = openai/gpt-oss-120b
TELEGRAM_BOT_TOKEN          = xxxx:xxxx
TELEGRAM_CHAT_ID            = -100xxxxxxx
BRAND_OS_SHEETS_ID          = spreadsheet_id
GOOGLE_SERVICE_ACCOUNT_JSON = {...}

---

## Google Sheets Structure

PersonalBrand-OS spreadsheet:

- brand_profile      — brand configuration
- brand_voice_rules  — content guidelines
- published_posts    — all published content log
- content_calendar   — planned content
- weekly_reports     — weekly performance data

---

## Known maintenance items to check periodically

- **Groq model deprecations**: Groq occasionally sunsets free-tier models with an email notice. Check `console.groq.com/docs/deprecations` every few months and re-point `GROQ_MODEL` (used in both `generate_brand_content` and `brand_voice_qa`) if `openai/gpt-oss-120b` is ever retired.
- **Pollinations.ai image generation**: image URLs are generated on request and can occasionally time out or 404. The dashboard now degrades gracefully (skeleton → fallback placeholder) if an image fails, but if failures become frequent, consider adding a retry/backoff step in `generate_brand_content` before the URL is written to `published_posts`.
- **Brand voice threshold drift**: if `brand_voice_qa` starts auto-approving content that reads off-brand, double-check the `always_do` / `never_do` rules in `brand_voice_rules` are still current — the QA layer only enforces what's in that sheet.

---

## Author

**Daffa Novendra Aditama**
AI Automation Engineer | Banten, Indonesia

[![LinkedIn](https://img.shields.io/badge/LinkedIn-daffanovendraaditama-blue)](https://linkedin.com/in/daffanovendraaditama)
[![GitHub](https://img.shields.io/badge/GitHub-Tama260-black)](https://github.com/Tama260)

---

*Built with Groq AI (openai/gpt-oss-120b) · Pipedream · Pollinations.ai · Google Sheets*
