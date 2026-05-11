# 🏦 Credit Follow-Up AI Agent

An automated, LLM-powered pipeline that processes overdue invoices, determines the appropriate escalation stage, and generates context-aware, professionally toned follow-up emails.

## 🏗️ Agent Architecture
The system uses a simple, deterministic sequential pipeline rather than a complex multi-agent framework. This ensures reliability, speed, and strict control over the output.

1. **Data I/O:** `pandas` loads mock invoice data from a CSV.
2. **Core Logic:** `tone_selector.py` calculates days overdue and maps it to one of 4 predefined tone stages (or flags for manual escalation).
3. **AI / LLM Layer:** `email_generator.py` passes the data and tone instructions to the Gemini API to draft the email.
4. **Execution:** `sender.py` processes the output (defaulting to a safe Dry-Run console print for testing).
5. **Audit:** Every action is strictly logged to `audit_log.json`.
6. **UI:** A lightweight `streamlit` dashboard visualizes the pipeline.

## 🤖 LLM Choice Rationale
This prototype utilizes **Google's Gemini 2.5 Flash** (upgraded from 1.5 Flash to handle recent API endpoint deprecations). 
* **Cost & Accessibility:** Offers a generous free tier perfect for prototyping.
* **Performance:** The "Flash" class models are highly optimized for fast, low-latency text generation. 
* **Capability:** It is more than capable of handling structured data-to-text generation tasks (like drafting standardized emails) without needing the heavier reasoning capabilities or higher costs associated with models like GPT-4o. 
* **Context Window:** The massive context window allows for highly detailed system prompting and few-shot examples if the project scales.

## 📝 Prompt Design
The agent uses a strict separation of instructions and data to prevent confusion and ensure consistent formatting.

**System Prompt (The Guardrails):**
```text
You are a professional finance collections assistant.
Generate a payment follow-up email at the TONE LEVEL specified.
Use ONLY the invoice data provided. Do not invent any numbers.
Respond ONLY in valid JSON with keys: "subject" and "body".
No preamble, no markdown fences.

Tone level: {stage} - {tone_label}
CTA: {cta}