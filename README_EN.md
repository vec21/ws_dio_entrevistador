# OpenClaw Interview Workshop — Technical Interviewer

This workshop teaches you how to build an AI-powered assistant that conducts structured technical interviews. It analyzes job postings, creates interview plans, asks questions one by one, evaluates responses, and generates final reports.

Everything runs locally on your machine using Docker. No Node installation, no loose scripts, no hassle.

---

## Workshop Creator

| | |
|---|---|
| LinkedIn | [linkedin.com/in/diogines-augusto/](https://www.linkedin.com/in/diogines-augusto/) |
| Instagram | [@dioaugusto.dev](https://www.instagram.com/dioaugusto.dev/) |

Feel free to reach out if you have questions, suggestions, or want to discuss the project.

---

## Free Models — Where to Get Them for Free

You don't need to spend money for this workshop. There are plenty of free options that work well.

### Workshop Recommendation: Google Gemini (Free)

The best free option today is Google AI Studio. You can get an API key without a credit card and with limits sufficient for the entire workshop.

| What | Detail |
|-------|---------|
| Where to create key | [aistudio.google.com](https://aistudio.google.com) |
| Recommended model | `google/gemini-2.5-flash` |
| Cost | Free |
| Need credit card? | No |
| Free limit | ~10 requests per minute, 250k tokens per minute |

How to configure in OpenClaw:
```bash
docker exec openclaw-workshop openclaw models set google/gemini-2.5-flash
```

### Other Free Options

| Provider | Model | Free Limit | Credit Card? | Where to get key |
|----------|--------|------------|--------------|------------------|
| Google AI Studio | Gemini 2.5 Flash | 10 RPM, 250k TPM | No | [aistudio.google.com](https://aistudio.google.com) |
| Google AI Studio | Gemini 2.5 Flash-Lite | 30 RPM, 1000 RPD | No | [aistudio.google.com](https://aistudio.google.com) |
| Groq | Llama 3.3 70B | 30 RPM, 14.4k RPD | No | [console.groq.com](https://console.groq.com) |
| Groq | Llama 4 Scout | 30 RPM, 14.4k RPD | No | [console.groq.com](https://console.groq.com) |
| OpenRouter | Various free models | Varies by model | No | [openrouter.ai](https://openrouter.ai) |

**Groq** is a good alternative—very fast responses and high-quality open-source models. Configure with `GROQ_API_KEY` in `.env`.

**OpenRouter** works as an "aggregator"—one API for dozens of models, including many free ones. Configure with `OPENROUTER_API_KEY` in `.env`.

### Paid Options (For Better Quality)

| Provider | Model | Approx. Cost | Where to get |
|----------|--------|--------------|--------------|
| OpenAI | GPT-4o | ~$2.50/1M input tokens | [platform.openai.com](https://platform.openai.com/api-keys) |
| Anthropic | Claude Sonnet 4 | ~$3.00/1M input tokens | [console.anthropic.com](https://console.anthropic.com) |
| Google | Gemini 2.5 Pro | Paid (removed from free tier in April 2026) | [aistudio.google.com](https://aistudio.google.com) |

For the workshop, Gemini 2.5 Flash works perfectly. Only pay if you want more sophisticated responses or production use.

---

## What is OpenClaw?

OpenClaw is a personal assistant that runs on your computer. You chat with it via text (WhatsApp, Telegram, Slack, Discord, terminal, etc.) and it responds using language models like [...]

The differentiator is that it's extensible via **skills**—text files that teach the assistant specific tasks. In this workshop, we create a skill that transforms OpenClaw into a technical interviewer.

---

## What are Skills?

A skill is a folder with a `SKILL.md` file inside. This file has two parts:

1. A YAML header with name and description
2. Text instructions telling the assistant how to behave

When OpenClaw starts, it reads all available skills and knows when to use each one. It's like giving someone a procedure manual—they read it and follow when the situation calls for it.

Example minimal skill:

```markdown
---
name: my_skill
description: Does something when asked.
---

# Instructions

When someone asks for X, do Y.
```

That's it. No code, no compilation, no dependencies.

---

## Prerequisites

You only need two things:

- **Docker Desktop** installed and running
- **An API key** from at least one model provider (OpenAI, Anthropic, or Google)

To check if Docker is installed, open terminal and run:

```bash
docker --version
```

If it shows something like `Docker version 27.x.x`, everything is good.

---

## Project Structure

```
workshop-openclaw/
├── docker-compose.yml          # Container configuration
├── .env.example                # Environment variables template
├── .env                        # Your keys (you create this)
├── skills/
│   └── interview-agent/
│       └── SKILL.md            # The interviewer skill
├── possiveiserros.md           # Troubleshooting reference
└── README.md                   # This file
```

---

## Step 1 — Configure Environment Variables

OpenClaw needs to know which model provider to use. This is done via environment variables.

Copy the example file and edit with your keys:

**In terminal:**
```bash
cp .env.example .env
```

**Or via interface:** Duplicate `.env.example`, rename to `.env`, and edit.

In `.env`, uncomment the line for your provider and paste your key:

```env
# If you use OpenAI:
OPENAI_API_KEY=sk-your-key-here

# If you use Anthropic (Claude):
# ANTHROPIC_API_KEY=sk-ant-your-key-here

# If you use Google (Gemini):
# GOOGLE_API_KEY=AIza-your-key-here
```

Only fill in **one** option.

### Where to Get Each Key?

| Provider | Where to get | Recommended model | Free? |
|----------|--------------|-------------------|-------|
| Google Gemini | [aistudio.google.com](https://aistudio.google.com) | gemini-2.5-flash | Yes, no card |
| Groq | [console.groq.com](https://console.groq.com) | llama-3.3-70b | Yes, no card |
| OpenRouter | [openrouter.ai](https://openrouter.ai) | various free | Yes, no card |
| OpenAI | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) | gpt-4o | No |
| Anthropic | [console.anthropic.com](https://console.anthropic.com) | claude-sonnet-4 | No |

---

## Step 2 — Run OpenClaw with Docker

With `.env` configured, run the container:

```bash
docker compose up -d
```

What this command does:
- Downloads the official OpenClaw image (first time)
- Creates a container named `openclaw-workshop`
- Mounts the `skills/` folder inside the container so OpenClaw finds the skill
- Exposes port 18789 for web access

To check if it started correctly:

```bash
docker compose logs -f
```

Wait until you see `Gateway listening on port 18789`. Then press `Ctrl+C` to exit logs.

### What is the Gateway?

The Gateway is OpenClaw's "brain". It:
- Receives your messages
- Decides which skill to use
- Sends the message to the language model
- Returns the response

Everything goes through it. When you "run OpenClaw", you're running the Gateway.

---

## Step 3 — Check if Skill is Loaded

Access OpenClaw's web interface at:

```
http://localhost:18789
```

In the interface, check available skills in the settings section. The `interview_agent` skill should appear in the list.

Alternatively, check via terminal:

```bash
docker exec openclaw-workshop openclaw skills list
```

Should show `interview_agent` in the list.

### If the skill doesn't appear?

1. Check if the path is correct: `skills/interview-agent/SKILL.md`
2. Check if the YAML header is valid (no tabs, with `---` at start and end)
3. Restart: `docker compose restart`

---

## Step 4 — Understand the Skill Before Using

Before testing, understand what the skill does. Open `skills/interview-agent/SKILL.md` and read.

The skill has 5 operation modes:

| Mode | What it does | When to use |
|------|--------------|-------------|
| Job Analysis | Reads a job posting and extracts competencies, responsibilities, and risks in JSON | When you have a job and want to understand what to evaluate |
| Interview Plan | Creates a script with stages, questions, and evaluation criteria | When you want to structure the interview |
| Conduction | Asks questions one by one and evaluates internally | When you want to simulate the full interview |
| Response Evaluation | Analyzes a specific response and gives score with justification | When you want feedback on an isolated response |
| Final Report | Consolidates everything into a report with recommendation | When the interview is over |

Each mode responds in structured JSON, which facilitates integration with other systems if you want to expand later.

---

## Challenge Enhancements (Adaptations for OpenClaw Challenge)

This version of the repository includes special adaptations for the DEV.to OpenClaw Challenge (April 2026):

- **Bilingual Support**: The skill responds in Portuguese or English automatically (detects from job descriptions) or can be forced with "switch to English".
- **Detailed Scoring**: Upgraded to 1-5 scale with sub-scores for technical, behavioral, and domain competencies.
- **Iterative Feedback Loops**: Added options during interview simulations to refine questions based on responses.
- **Enhanced JSON Structures**: Richer outputs for easier API integration (e.g., added fields like `idioma_principal`, `dificuldade_estimada`, `feedback_sugestao`).

Test with English prompts to see language switching. Example: "Use the interview_agent skill to analyze this job in English."

---

## Step 5 — Test: Analyze a Job

Let's start the first test. In OpenClaw's web interface (or terminal), send this message:

### Via web interface

Access `http://localhost:18789`, open chat, and paste:

```
Use the interview_agent skill to analyze this job in JSON:

Senior Backend Engineer - Fintech

Responsibilities:
- Build critical payment APIs
- Work with microservices and event-driven architecture
- Ensure observability, scalability, and reliability
- Handle production incidents
- Collaborate with product, security, and infrastructure

Requirements:
- Experience with Java, Kotlin, or Go
- Messaging with Kafka or RabbitMQ
- Relational and NoSQL databases
- Cloud AWS or GCP
- Strong technical communication skills

Differentials:
- PCI-DSS experience
- Fraud knowledge
- Experience in regulated environments
```

### Via terminal

```bash
docker exec openclaw-workshop openclaw agent --message 'Use the interview_agent skill to analyze this job in JSON:

Senior Backend Engineer - Fintech

Responsibilities:
- Build critical payment APIs
- Work with microservices and event-driven architecture
- Ensure observability, scalability, and reliability
- Handle production incidents

Requirements:
- Java, Kotlin, or Go
- Kafka or RabbitMQ
- Relational and NoSQL databases
- AWS or GCP'
```

### What to expect

A JSON decomposing the job structure (now with additional fields like `idioma_principal` and `dificuldade_estimada`):

```json
{
  "cargo": "Senior Backend Engineer",
  "senioridade": "senior",
  "contexto_negocio": "Fintech",
  "idioma_principal": "en",
  "responsabilidades_principais": ["..."],
  "competencias_tecnicas": ["..."],
  "competencias_comportamentais": ["..."],
  "conhecimento_dominio": ["..."],
  "temas_entrevista": ["..."],
  "sinais_risco": ["..."],
  "dificuldade_estimada": "alta"
}
```

This is useful because it transforms a generic job into a clear map of what needs evaluation.

---

## Step 6 — Test: Generate Interview Plan

Now ask to create the script:

```
Use the interview_agent skill and generate a technical interview plan for the Senior Backend Engineer job in Fintech.

The interview should last 60 minutes. Respond in JSON.
```

### What to expect

A plan with stages, estimated times, suggested questions, and evaluation criteria (now in 1-5 scale) for each.

This solves a real problem: creating consistent interviews. Instead of each interviewer inventing questions on the spot, you have a standardized script.

---

## Step 7 — Test: Simulate Full Interview

This is the most interesting test. Send:

```
Use the interview_agent skill.

I want to start a technical interview in candidate mode.

Job: Senior Backend Engineer - Fintech

Responsibilities:
- Build critical payment APIs
- Work with microservices and event-driven architecture
- Ensure observability, scalability, and reliability
- Handle production incidents

Requirements:
- Java, Kotlin, or Go
- Kafka or RabbitMQ
- Relational and NoSQL databases
- AWS or GCP
- Strong technical communication
```

### What to expect

The assistant will ask the first question in format:

```
Question 1:
Tell me about a critical backend system you've worked on. What was the business problem, what architecture decisions did you make, and how did you ensure it worked well in production?

What I'm observing:
Practical experience with critical systems, communication clarity, technical trade-offs, and operational maturity.
```

### How to continue

Respond as if you were the candidate. For example:

```
I worked on a recurring payment API. We had issues with duplicate charges during retries, so we implemented
idempotency keys, structured logs, and transaction status metrics.
```

The assistant will evaluate internally and ask the next question. Keep responding until you want to end.

### Why this is useful?

- To train candidates before real interviews
- To calibrate interviewers (everyone sees the same questions and criteria)
- To document the evaluation process

---

## Step 8 — Test: Evaluate Isolated Response

If you want to evaluate a specific response without running the full interview:

```
Use the interview_agent skill to evaluate this response.

Question:
Tell me about a critical backend system you've worked on.

Response from candidate:
I worked on a recurring payment API. The biggest problem was avoiding duplicate charges during retries and network failures. We used idempotency keys per operation, database transactions for consistency, async processing queues, and dashboards with error rate metrics, latency, and transaction status. We also had runbooks for incidents.

Respond in JSON.
```

### What to expect

```json
{
  "pergunta": "...",
  "resumo_resposta": "...",
  "competencia_avaliada": "...",
  "nota_geral": 4,
  "sub_scores": {
    "tecnico": 5,
    "comportamental": 4,
    "dominio": 3
  },
  "sinais_positivos": ["..."],
  "sinais_negativos": ["..."],
  "sinais_ausentes": ["..."],
  "pergunta_complementar": "...",
  "feedback_sugestao": "..."
}
```

The evaluation separates positive, negative, and absent signals. This helps understand not just "how well," but exactly where the candidate excelled and where evidence was lacking.

---

## Step 9 — Test: Generate Final Report

After some questions and answers, ask for the report:

```
Use the interview_agent skill to generate final report.

Context:
Job: Senior Backend Engineer in fintech.

Observed evidence:
1. Candidate explained use of idempotency keys, retries, transactions, and queues in payment system.
2. Demonstrated familiarity with observability: error metrics, latency, and transaction status.
3. Mentioned runbooks and incident handling.
4. Little evidence on technical leadership, security collaboration, and PCI-DSS compliance experience.

Respond in JSON.
```

### What to expect

A report with clear recommendation ("advance", "in doubt", or "do not advance"), confidence level, strengths, risks, and suggested next steps (now with sub-scores and general feedback).

---

## Flow Summary

```
1. Configure API key          (.env)
2. Run container               (docker compose up -d)
3. Check skill loaded          (web interface or terminal)
4. Analyze a job               (Mode 1)
5. Generate interview plan     (Mode 2)
6. Simulate full interview     (Mode 3)
7. Evaluate individual responses (Mode 4)
8. Generate final report       (Mode 5)
```

---

## Troubleshooting

### Container won't run

```bash
# Detailed logs
docker compose logs openclaw

# Check if port is free
docker ps -a
```

Common causes:
- Port 18789 in use by another service
- Docker Desktop not running
- `.env` file with formatting errors

### Model doesn't respond or auth error

Verify the API key in `.env` is correct. Test the key directly on the provider's site.

If using a specific model, you may need to configure:

```bash
docker exec openclaw-workshop openclaw models set <provider>/<model>
```

Examples:
```bash
docker exec openclaw-workshop openclaw models set openai/gpt-4o
docker exec openclaw-workshop openclaw models set anthropic/claude-sonnet-4
docker exec openclaw-workshop openclaw models set google/gemini-2.5-flash
```

### Skill doesn't appear

1. Check path is correct: `skills/interview-agent/SKILL.md`
2. Check YAML header is valid (no tabs, with `---` at start and end)
3. Restart: `docker compose restart`

### Reset model (if configured wrong)

```bash
docker exec openclaw-workshop openclaw models set google/gemini-2.5-flash
docker exec openclaw-workshop openclaw models fallbacks clear
docker exec openclaw-workshop openclaw models status
```

---

## Going Further

- Create new skills for other scenarios (code review, onboarding, retrospectives)
- Connect OpenClaw to Telegram or Slack for mobile use
- Explore [ClawHub](https://clawhub.ai) for community-ready skills
- Build composite skills that call other skills

---

## References

- [Official OpenClaw Repository](https://github.com/openclaw/openclaw)
- [Skills Documentation](https://github.com/openclaw/openclaw/blob/main/docs/tools/skills.md)
- [ClawHub — Skills Registry](https://clawhub.ai)
- [Docker Setup Guide](https://code-boost.com/how-to-install-openclaw-with-docker-step-by-step/)