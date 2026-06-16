You are converting a BMAD-METHOD skill folder into a single pure-markdown `SKILL.md` that Paperclip can import and continue using statelessly. Paperclip's skill loader only reads `SKILL.md` — it ignores `customize.toml`, `.py`, and `.yaml` files.

**Input:** the BMAD skill folder I'm attaching (contains at minimum `SKILL.md` and `customize.toml`; may also reference `_bmad/scripts/resolve_customization.py` or `_bmad/bmm/config.yaml`).

**Output:** one self-contained `SKILL.md` with valid Paperclip frontmatter and all behavior inlined. Do not produce any other files.

**Rules:**

1. Keep the YAML frontmatter `name` and `description` from the original `SKILL.md`. Rewrite `description` so Paperclip can match it on intent (e.g. "Use when the user asks for market research, competitive analysis, or trend assessment").
2. Delete every reference to `resolve_customization.py`, `customize.toml`, `_bmad/custom/*.toml`, `_bmad/bmm/config.yaml`, `{project-root}`, `{skill-root}`, and the activation-step machinery. Paperclip skills are stateless — there is no resolver, no config file, no prepend/append hooks.
3. Inline the `[agent]` block from `customize.toml` into a `## Persona` section: name, title, icon, role, identity, communication_style, and the `principles` list as bullets. Inline `persistent_facts` as a `## Always Remember` section, dropping any `file:` entries that point outside the skill.
4. Inline `[[agent.menu]]` entries (for router skills like `bmad-agent-analyst`) as a `## Menu` section: a markdown table with columns `Code | Description | Invokes`, where `Invokes` is the sibling skill's `name` (Paperclip will route to it when imported).
5. Replace BMAD's "Step 1 … Step 8" activation flow with a short `## On Activation` section: greet the user, adopt the persona, present the menu (if any), wait for input. No file loads, no script calls.
6. For methodology skills (everything except `bmad-agent-analyst`): keep the full methodology body — frameworks, prompts, checklists, output templates — verbatim. The methodology is the value.
7. Strip all BMAD-internal jargon that won't resolve in Paperclip: `{user_name}`, `{communication_language}`, `{document_output_language}`, `{planning_artifacts}`, `{project_knowledge}`. Replace with neutral instructions ("greet the user warmly," "ask the user which language to respond in," etc.).
8. Preserve continuability: at the end of every workflow step, instruct the agent to **summarize state in plain prose** before asking the next question, so a future Paperclip session can resume by re-reading the conversation. Do not rely on sidecar files.
9. Output only the final `SKILL.md` content in a single fenced code block. No commentary.