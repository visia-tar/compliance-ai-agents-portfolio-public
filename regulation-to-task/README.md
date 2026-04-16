# Regulation-to-Task Pipeline (Prototype)

### **Executive Summary**
This project demonstrates a "Compliance-as-Code" architecture designed to bridge the gap
between regulatory requirements and engineering execution. It utilizes a Large Language Model (Claude 4.6) 
to automate the decomposition of complex regulatory text into structured, actionable engineering tasks (JSON).

The **Regulation-to-Task Pipeline** acts as an automated triage layer. 
It ingests raw regulatory text and outputs structured data that ensures 1:1 traceability 
between a legal clause and a technical ticket.

**Key Architectural Features:**
* **Structured Decomposition:** Translates dense legal prose into clear "Definition of Done" criteria for developers.
* **JSON Schema Enforcement:** Generates outputs ready for direct API integration with project management tools
  like Jira, GitHub Issues, or ADO.
* **Traceability Mapping:** Automatically attaches the specific regulatory citation
  to every technical task to simplify future audits.
* **Developer-Centric Focus:** Reduces friction for engineering teams
  by providing them with clear technical requirements rather than legal documents.

### **Logic Workflow**
1.  **Ingestion:** Raw text or specific regulatory articles are provided to the agent.
2.  **Contextual Mapping:** The agent identifies which technical domains (Data, Security, UI/UX, Logging) are impacted by the clause.
3.  **Task Synthesis:** The agent drafts a technical requirement, including specific validation criteria.
4.  **Export:** A structured JSON object is generated for review and ticket creation.
