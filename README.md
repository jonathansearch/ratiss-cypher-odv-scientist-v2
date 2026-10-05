<p align="center">
  <img src="docs/assets/logo.png" alt="RATISS Labs logo" width="180"/>
</p>

[![RATISS Labs](https://img.shields.io/badge/RATISS_Labs-Deep_Tech_Sovereign-06b6d4)](https://github.com/jonathansearch)

# RATISS CYPHER ODV SCIENTIST V2

**RATISS V9 Aeon Prime autonomous scientific agent** — secure multi-user interface deployed on Hugging Face Spaces (Docker mode).

> Intellectual Property: **JohnKing0 & Architect Jonathan Evina**
> ORCID: [0009-0000-4092-5313](https://orcid.org/0009-0000-4092-5313) — DOI: [10.17605/OSF.IO/6JZMB](https://doi.org/10.17605/OSF.IO/6JZMB)

---

## 1. Architecture

The system is organized in four main layers. The **interface** layer relies on Chainlit and exposes a French-language conversation with a REACT agentic loop. The **LLM** layer queries NVIDIA **Nemotron 3 Ultra** (550B A55B, `:free` version) through OpenRouter, with `openrouter/auto` as fallback configured by the `OPENROUTER_MODEL` variable. The **scientific brain** layer contains the RATISS core: Lanczos diagonalization (exact t-J model), persistent homology (Betti numbers) on protein structures, ZK-STARK receipts, and the TransDIPL'Y semantic routing with the Pantheon of 30 Peers. Finally, the **native security** layer guarantees isolated UUID sessions with dedicated workspaces and token storage in SHA-256 hash only.

| Folder | Role |
|---|---|
| `app.py` | Chainlit interface + REACT agentic loop (HF Spaces entry point) |
| `src/ratiss_v9_aeon_prime/` | RATISS scientific brain (physics, topology, agent, CLI) |
| `src/connectors/` | Quantum connectors (IBM Quantum, Quandela, PennyLane, CPU fallback) |
| `security/` | Session manager (UUID, SQLite, isolation) and SHA-256 token vault |
| `scripts/` | `import_skill.py` (GitHub skill import) and `align_agent.py` (Nemotron alignment) |
| `docs/` | Agentic memory `AGENTS.md` |

## 2. Native security

Each user gets at login a **UUID4 session** with a strong access token that is handed over only once and never stored in clear: only its **SHA-256 hash** is persisted in `data/sessions.db`. Workspaces are strictly isolated (`workspace/{session_id}/`) and sessions expire by default after 24 hours (configurable via `SESSION_TTL_HOURS`). API keys are verified by hash comparison (`security/token_vault.py`) and never transit through logs or the agent's answers.

## 3. Hugging Face Spaces deployment

1. Create a new Space in **Docker** mode on [huggingface.co/spaces](https://huggingface.co/spaces).
2. Link the GitHub repository `ratiss-cypher-odv-scientist-v2` (Settings → Linked resources).
3. Add the repository **secrets** (Settings → Repository secrets):

| Secret | Value |
|---|---|
| `OPENROUTER_API_KEY` | OpenRouter key (starting with `sk-or-v1-`) |
| `OPENROUTER_MODEL` | `nvidia/nemotron-3-ultra-550b-a55b:free` |
| `OPENROUTER_BASE_URL` | `https://openrouter.ai/api/v1` |
| `CHAINLIT_AUTH_SECRET` | JWT secret (generable with `chainlit create-secret`) |
| `IBM_QUANTUM_TOKEN` | (optional) IBM Quantum token |
| `QUANDELA_API_TOKEN` | (optional) Quandela token |

4. The Space starts automatically on port 7860.

## 4. Local usage

```bash
# Configuration
cp .env.example .env      # edit .env with your keys
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt

# Agent alignment (checks keys + brain + Nemotron)
python3 scripts/align_agent.py --full

# Launching the interface
CHAINLIT_AUTH_SECRET=$(chainlit create-secret) chainlit run app.py --port 8000
```

## 5. Exposed scientific tools

| Tool | Description |
|---|---|
| `solve_quantum` | Full RATISS pipeline: Lanczos t-J, persistent homology, ZK-STARK receipt |
| `route_task` | TransDIPL'Y semantic routing (domain, solver, expert peers) |
| `pdb_meta` | RCSB PDB metadata of a protein structure |
| `health` | Node diagnostics (RAM, 7.5 GB Memory Guard) |

## 6. Importing new skills

```bash
python3 scripts/import_skill.py https://github.com/owner/repo.git [--name name]
```

The script clones the repository into `skills/`, detects the entry point, installs the dependencies and generates a standardized `skill_config.json` consumed by the orchestrator.
