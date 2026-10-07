# Postmortem Agent Skill

A portable [Agent Skill](https://agentskills.io/specification) for writing,
updating, and auditing blameless, evidence-based postmortems (`PM-NNN`
records) for production and staging incidents, near misses, data-integrity
or privacy failures, rollbacks, and repeated regressions.

The skill refuses to fabricate: every timestamp, cause, identifier, and
negative claim must come from an opened source, and an unsupported-claim
scrub runs as an edit gate before a record is saved. A record stays `Draft`
until the incident window, impact, causes, durable fix, controls, rollout,
and user-visible healing are all evidenced.

## Skills

- `postmortem`: create, update, and audit postmortems and maintain
  `docs/postmortems/INDEX.md`.

It keeps incident history distinct from Architecture Decision Records, Feature
Decision Records, stories, runbooks, and changelogs, and pairs well with the
[ADR and FDR skills](https://github.com/thiagofgf/adr-fdr-skills).

## Install

### Codex

Ask Codex to install the skill from this repository, or run:

```bash
python "${CODEX_HOME:-$HOME/.codex}/skills/.system/skill-installer/scripts/install-skill-from-github.py" \
  --repo thiagofgf/postmortem-skill \
  --path .agents/skills/postmortem
```

The skill becomes available in the next Codex turn.

### Claude Code

Clone once and link the skill into the user-level discovery directory:

```bash
git clone https://github.com/thiagofgf/postmortem-skill.git ~/.local/share/postmortem-skill
mkdir -p ~/.claude/skills
ln -s ~/.local/share/postmortem-skill/.agents/skills/postmortem ~/.claude/skills/postmortem
```

### Repository-local agents

Copy `.agents/skills/postmortem` into the repository and create
`docs/postmortems/INDEX.md` (the skill documents its table format). For Claude
Code compatibility, link `.claude/skills` to `../.agents/skills`.

## Layout

```text
.agents/skills/
└── postmortem/
    ├── SKILL.md
    ├── references/template.md
    └── evals/evals.json
.claude/skills -> ../.agents/skills
```

`.agents/skills` is the only canonical source; the Claude path is a symlink so
the skill definition cannot drift. `evals/evals.json` holds behavior checks
for the skill itself (classification, evidence-skeleton mode, near misses).

## License

Distributed under the [MIT License](LICENSE).
