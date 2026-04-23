# Compliance Agent 🏛️

An AI-powered agent that translates regulatory provisions into structured JSON engineering tasks. Built with Python and Claude (Anthropic), it helps Technical Program Managers bridge the gap between legal/compliance language and actionable technical work.

---

## What it does

You give it a regulation in plain English. It gives back a structured JSON telling your engineering team what they need to build or change to meet that requirement. It does not prescribe exactly how, unless optional parameters are passed which detail the architecture or tools/systems used by engineering.

**Input:**
```
"All logs must be retained for 7 years."
```

**Output:**
```json
{
  "requirement_summary": "Audit logs must be stored and preserved for a minimum of 7 years.",
  "regulation_domain": "Security",
  "risk_level": "High",
  "engineering_tasks": [
    {
      "task_id": "TASK-001",
      "title": "Implement log retention policy",
      "description": "Ensure all system and audit logs are stored in a durable, immutable storage system with a minimum retention period of 7 years. Logs must be protected against modification or deletion.",
      "area": "Logging"
    }
  ],
  "open_questions": [
    "Which log types are in scope — application, infrastructure, security, or all?",
    "Does the 7-year clock start at log creation or at end of the related business transaction?"
  ]
}
```

---

## Project structure

```
NEW-Regulation-To-Task/
│── new_complianace_translator.py     # Main agent — all logic lives here
│── README.md               # This file
│── venv/                   # Python virtual environment (not committed to git)
│── output/                 # Auto-created on first run, holds result files
    │── compliance_tasks_YYYY-MM-DD_HH-MM-SS.json
```

---

## Architecture

```
-----------------------------------------------------------
│                  compliance_agent.py                    │
│                                                         │
│  1. argparse        > reads CLI flags                   │
│  2. load_regulations_from_file()  > parses .txt /       │
│                                     .json / .jsonl      │
│  3. build_user_message()  > assembles the prompt with   │
│                              optional context fields    │
│  4. translate_policy()    > calls Claude API            │
│  5. save_results()        > writes JSON to output/      │
-----------------------------------------------------------
                         │  HTTPS API call
                         V

-----------------------------------------------------------
│               Anthropic API (Claude)                    │
│                                                         │
│  Model : claude-sonnet-4-6                              │
│  Role  : Senior TPM reading the regulation and          │
│          returning structured JSON tasks                │
-----------------------------------------------------------
                         │  JSON response
                         V
-----------------------------------------------------------
│               output/ folder                            │
│   compliance_tasks_2025-06-01_14-32-10.json             │
-----------------------------------------------------------
```

---
## Usage

### Single regulatory text

```bash
python3 compliance_agent.py --text "All logs must be retained for 7 years"
```

### From a text file (one regulation per line)

```bash
python3 compliance_agent.py --file regulations.txt
```

### From a JSON file (with per-regulation context)

```bash
python3 compliance_agent.py --file regulations.json
```

### It is possible to pass optional context flags

---

## Input formats

### `.txt` — one regulation per line
```
All logs must be retained for 7 years.
User data must be encrypted at rest.
Access to production must require MFA.
```

### `.json` — list of strings
```json
[
  "All logs must be retained for 7 years.",
  "User data must be encrypted at rest."
]
```

### `.json` — list of objects with per-regulation context
```json
[
  {
    "text": "All logs must be retained for 7 years.",
    "jurisdiction": "Federal US",
    "product_context": "Financial SaaS platform"
  },
  {
    "text": "Personal data must not leave the EU.",
    "jurisdiction": "EU",
    "tech_stack": "AWS eu-west-1, Kafka, S3"
  }
]
```

### `.jsonl` — one JSON object per line (good for large batches)
```
{"text": "All logs must be retained for 7 years.", "jurisdiction": "Federal US"}
{"text": "Personal data must not leave the EU.", "jurisdiction": "EU"}
```

---

## CLI flags reference

| Flag | Short | Required | Description |
|---|---|---|---|
| `--text` | `-t` | Yes (or `--file`) | Single regulation as a string |
| `--file` | `-f` | Yes (or `--text`) | Path to input file |
| `--jurisdiction` | `-j` | No | e.g. `"EU"`, `"Federal US"`, `"California"` |
| `--product-context` | `-p` | No | e.g. `"SaaS B2B analytics platform handling PII"` |
| `--tech-stack` | `-s` | No | e.g. `"AWS, Python, PostgreSQL"` |
| `--output-dir` | `-o` | No | Where to save results (default: `./output`) |

---

## Optional context fields — do I need them?

No. The agent works without any of them. But the more context you provide, the more specific and useful the output becomes.

| Field | Without it | With it |
|---|---|---|
| `--jurisdiction` | Claude infers framework from regulation text | Claude references the correct legal framework (GDPR, HIPAA, SOC 2) |
| `--product-context` | Generic tasks | Tasks scoped to your product type |
| `--tech-stack` | Technology-agnostic language | Tasks reference your actual systems |

---

## Output

Each run creates a timestamped JSON file in `./output/`:

```
output/compliance_tasks_2025-06-01_14-32-10.json
```

The file is a JSON array — one object per regulation processed. Each object includes the `_source_text` field so you can always trace the output back to its input.

---

## Important notes

- **The output is a first draft.** Always review with engineering leads before moving tasks to a sprint. Claude does not know your existing controls, team structure, or actual system topology.
- **API costs apply.** Each regulation processed = one API call. For large batches, check your [Anthropic usage dashboard](https://console.anthropic.com).

---
---

## Legal Notice
This tool is provided for informational purposes only and does not 
constitute legal advice. Output produced by this tool should be 
validated by a qualified professional before being relied upon for 
regulatory compliance purposes. The authors accept no liability for 
decisions made on the basis of this tool's output.

---

## License

Copyright (c) 2025 visia-tar. All rights reserved.

This repository is shared for review purposes only. 

Please reach out if you'd like to discuss collaboration or usage.
---

