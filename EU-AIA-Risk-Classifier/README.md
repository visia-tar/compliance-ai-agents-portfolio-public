# EU AI Act Risk Classification Agent
An intelligent compliance agent that automatically classifies AI systems into the 
four risk tiers defined by the EU AI Act (2024). Built for product and engineering 
teams who need fast, auditable, regulator-ready risk assessments at the start of 
every AI project.

---

## Project structure
```
EU-AIA-Risk-Classifier/
├── classifier/
│   ├── __init__.py
│   ├── agent.py          # core agent logic
│   ├── prompts.py        # AIA-grounded system prompts
│   └── report.py         # report generator
├── tests/
│   ├── test_agent.py
│   └── fixtures/         # sample product docs for testing
│       ├── high_risk.txt
│       ├── minimal_risk.txt
│       └── ambiguous.txt
├── reports/              # folder with output - JSON - files
├── main.py               # CLI entrypoint
├── requirements.txt
└── .env
```

---

## How the classification works

This agent brings AIA legal reasoning directly into the development workflow, so risk is 
assessed early, consistently, and with a clear audit trail. 


**The agent doesn't guess.** It follows a structured decision tree mapped directly 
to the EU AI Act — checking Art. 5 prohibited uses, matching against 
Annex III high-risk categories, and applying Art. 52 transparency obligations — 
before arriving at a classification. Every output cites the specific articles and 
annexes that drove the decision.

---

## Risk tiers

| Tier | AIA Reference | Description | Agent behavior |
|---|---|---|---|
| 🔴 UNACCEPTABLE | Art. 5 | Prohibited systems | Hard block + immediate escalation |
| 🟠 HIGH | Annex III | Conformity assessment required | Flags for compliance review |
| 🟡 LIMITED | Art. 52 | Transparency obligations apply | Notes disclosure requirements |
| 🟢 MINIMAL | — | No specific AIA obligations | Passes with documentation |

---

### Architecture

**1. Input layer**
Accepts product documentation from the CLI, a Jira webhook, or a GitHub trigger.

**2. Agent layer**
Two files work together:
- `prompts.py` — encodes the AIA legal knowledge (Art. 5, Annex III, Art. 52) and the decision tree
- `agent.py` — sends the document and prompt to the Claude API and parses the response

**3. Model layer**
Claude Sonnet reads the document, reasons against the AIA decision tree, and returns a structured JSON classification.

**4. Output layer**
Two outputs are generated simultaneously:
- A rich **terminal report** for the engineer — human readable, color coded by risk tier
- A **JSON file** for regulators and compliance teams — machine readable, timestamped, audit ready

**5. Routing layer**
Based on the classification:

| Result | What happens next | 
|---|---|
| 🟢 MINIMAL | Passes automatically, classification logged |
| 🟡 LIMITED | Passes with transparency requirements noted |
| 🟠 HIGH | Routed to compliance reviewer for sign-off |
| 🔴 UNACCEPTABLE | Hard blocked, security team notified |
| ⚑ AMBIGUOUS | Flagged for human review regardless of tier |

---

### POSSIBLE ENHANCEMENTS
- Right now the output is a JSON file saved to a reports/ folder.
  There is a significant opportunity to integrate with enterprise tooling
  such as Slack, Email, Jira, and transform these reports into actionable alerts and tasks
- Manage long documents in input. Modify prompts.py and instruct the agent to focus on specific sections
- Read PDF files
- The agent defaults to `claude-sonnet-4-5`. Consider passing to a more advanced,
  but more expensive, model like Opus modifying the `--model` flag in  agent.py.

---

## Legal Notice
This tool is provided for informational purposes only and does not 
constitute legal advice. Classifications produced by this tool should be 
validated by a qualified legal professional before being relied upon for 
regulatory compliance purposes. The authors accept no liability for 
classification decisions made on the basis of this tool's output.

---

## License
This project is currently unlicensed while in early development.
Contact visia-tar for usage permission

---
