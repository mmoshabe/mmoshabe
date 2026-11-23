### Building AI systems that are safe to put in front of customers 🇿🇦

I design, build and roll out AI automations for South African businesses. My focus is the part that's usually skipped: **evals, human oversight, cost control and POPIA**, so a system that works in a demo also works on a Monday morning.

---

#### 🔧 AI engineering: [ai-support-triage](https://github.com/mmoshabe/ai-support-triage)
Multi-agent support triage on Claude: a classifier routes each ticket, a drafter answers from the help centre, a critic checks the draft, and anything risky goes to a human in Slack with one-click **Approve & send**.

- **96.8% routing accuracy** on a held-out set I never tuned against (100 labelled tickets with written labelling rules)
- **$0.018 per ticket**, measured; Sonnet halves it, with the trade-off documented
- Fails closed: errors, refusals, prompt injection, refunds and security tickets always reach a human
- Python · Anthropic SDK · FastAPI · Vercel · Neon Postgres · Slack · Zendesk webhooks · GitHub Actions

#### 🧭 AI engineering: [job-copilot](https://github.com/mmoshabe/job-copilot)
A job-search copilot I use myself. It scores job posts against my CV requirement by requirement, writes cover letters that a second agent **fact-checks against the CV**, and tracks applications with follow-up reminders.

- The eval's headline metric is **fabricated claims**. The fact-checker caught subtle inflations ("3 teams" → "3 teams *outside my own*"), and a wording rule took them to 0
- Prompt-injection test: a job post telling the AI to claim "8 years Kubernetes" never got through
- Python · Anthropic SDK · Greenhouse/Lever APIs · SQLite

#### 📈 AI strategy & governance: [ai-adoption-playbook](https://github.com/mmoshabe/ai-adoption-playbook)
The business side of the same system: options and build vs buy, an ROI model with sensitivity analysis, a gated rollout (shadow → assisted → auto), and a one-page POPIA-aware governance checklist.

- **About 3-month payback** at 1,500 tickets a month, and **don't build below about 300 a month**
- LLM spend is about 3% of the running cost; help-centre coverage is the real lever

#### 💬 Implementation: [ai-booking-assistant](https://github.com/mmoshabe/ai-booking-assistant)
Live at a real South African beauty salon. A website enquiry reaches the owner seconds later, already sorted, with a suggested reply drafted from the salon's own price list and logged to a Google Sheet. A person always sends the reply.

- **Guardrails in code, not in the prompt:** every quoted price must be on the price list, a draft that confirms a booking is flagged, and so is a mismatched weekday and date. Complaints and refunds always go to the owner
- **~$0.001 per enquiry**, hosted for **$0/month** (Oracle Always Free + Caddy HTTPS), least-privilege Google scopes (send-only Gmail)
- Knowledge base built from the salon website's own content, so the owner's price edits flow straight into the answers
- n8n (workflow generated from code) · Claude Haiku 5.5 · Google Sheets & Gmail APIs · Astro · Vercel · Docker · POPIA

---

**How I work:** measure before claiming, keep humans in the loop where mistakes cost money, and write down the trade-offs.

**Stack:** Python · TypeScript · Claude / Anthropic API · FastAPI · n8n · Postgres · Vercel · Slack & Google APIs · Docker
