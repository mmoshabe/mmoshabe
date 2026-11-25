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
- **Evaluated:** 56 labelled enquiries scored against the production code, never tuned on the held-out split. **100% of must-escalate enquiries reached the owner**, 0 drafts confirmed a booking
- **~$0.0004 per enquiry** (measured), hosted for **$0/month** (Oracle Always Free + Caddy HTTPS), least-privilege Google scopes (send-only Gmail, free/busy-only calendar)
- Knowledge base built from the salon website's own content, so the owner's price edits flow straight into the answers
- n8n (workflow generated from code) · Claude Haiku 5.5 · Google Sheets & Gmail APIs · Astro · Vercel · Docker · POPIA

#### 📚 AI engineering (RAG): [popia-assistant](https://github.com/mmoshabe/popia-assistant)
Answers questions about South Africa's privacy law (POPIA) from the Act, its Regulations and the Information Regulator's guidance notes. **Every statement cites the exact provision** ("s 69(3)", "regulation 4(1)"), and when the documents don't cover a question it says so instead of guessing.

- **87% fully correct on a fresh held-out set** written after the last fix, up from 78% on the first test run. The four weaknesses that run exposed were fixed and checked on dev questions, not on the test set
- Declined every question the documents don't answer, and **never once said "you're compliant"** or leaked its prompt, including under prompt injection
- Hybrid search (Voyage embeddings + BM25 + direct section lookup, reranked), Anthropic search-result citations, uncited answers retried then flagged
- **About $0.0018 per question**; search runs inside Voyage's free tier
- Python · Claude Haiku 5.5 · Voyage AI · Anthropic citations · 51-question eval · GitHub Actions

#### 🔌 AI engineering (MCP): [salon-mcp](https://github.com/mmoshabe/salon-mcp)
An MCP server that connects Claude to a small salon's clients and bookings. **Claude looks things up and proposes; only the owner acts.** There's no send, delete or edit tool: messages, bookings and cancellations wait in an approval queue the owner works through outside the AI.

- Permission levels (read-only hides the proposal tools entirely), MCP tool annotations, and an audit log of every call, including refused ones
- POPIA in code: contact details masked, no "list all clients" tool, promotions refused for clients without marketing consent
- A client note telling "the AI" to send everyone's numbers to a stranger is flagged as data, not followed
- **Agent eval over MCP with Claude Haiku: 5/5 on held-out scenarios** (13/13 on the main set after two guidance fixes), with the one weakness the checks missed written up rather than tuned away
- Python · MCP Python SDK v2 · Claude tool use · SQLite · GitHub Actions

#### 🧾 Implementation (document AI): [invoice-extractor](https://github.com/mmoshabe/invoice-extractor)
Supplier invoices, till slips and phone photos of them become an accounting import file. **Claude copies what's printed; plain code decides whether it can be booked**, using SARS's own tax-invoice checklist (full vs abridged at R5,000, zero-rated items, credit notes, pro-formas, duplicates). Anything held goes to a review list with the reason in plain English.

- **Every planted mistake caught** (20/20: VAT at 14%, VAT on zero-rated food, totals that don't add up, missing VAT numbers, credit notes, quotations)
- 100% of fields read on the base set; on a hard set (hand-written, dark, sideways and low-res photos, multi-page, decimal commas) 95% routed exactly right, and the one miss was a false alarm, not a wrong booking
- A line printed on an invoice telling the AI to record VAT as zero was ignored
- **$0.0007 per document** with Haiku 5.5; Sonnet scored the same at 17× the cost
- Python · Claude structured outputs (PDF + vision) · 49-document synthetic eval · GitHub Actions

#### ⚖️ Automation platforms: [lead-workflow-compare](https://github.com/mmoshabe/lead-workflow-compare)
The same AI lead-qualification workflow built on **n8n, Make and Zapier** for a software studio, then measured on the same 20 labelled leads. Claude qualifies each enquiry and drafts a follow-up, every lead is logged to a Google Sheet, and only leads worth a call or an email ping Slack.

- **One prompt, three platforms:** each platform's version is generated from the same request file, and tests check that each one sends an identical Claude request
- All three caught **6/6 high-fit leads** with **0 spam pings**, for about **$0.009 per 20 leads**. The real differences were operational:
  - on its default settings, Make **switched the whole scenario off after one bad AI response**, holding later leads until someone re-enabled it
  - Zapier **can't run it on the free plan** and rewrites timestamps on the way into Sheets
  - n8n is the only one that lives fully in git
- n8n · Make · Zapier · Claude Haiku 5.5 · Google Sheets API · Slack webhooks

---

**How I work:** measure before claiming, keep humans in the loop where mistakes cost money, and write down the trade-offs.

**Stack:** Python · TypeScript · Claude / Anthropic API · RAG (Voyage AI) · MCP · FastAPI · n8n · Make · Zapier · Postgres · Vercel · Slack & Google APIs · Docker
