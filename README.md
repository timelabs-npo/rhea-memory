# Rhea Memory

**Stop paying for the same forgetting.**

Rhea Memory is a small Python library and CLI for persistent agent context: SQLite facts and events, plus a separate generator that turns selected project activity into a compact text feed.

A session ends. The next agent spends its first thousand words rediscovering the project name, the open task, and yesterday's decision. Forgetting has become a recurring expense. Put the useful facts somewhere the next process can read.

**Current form:** local SQLite storage and project-summary code. Ordinary persistence, inspectable methods, no model service required to store a fact or generate a feed.

## Two tools, two jobs

| Tool | Input | Result |
|---|---|---|
| `MemoryStore` | A data directory | Key/value facts and an ordered event timeline in `memory.db` |
| `MemoryFeed` | A project root | Text assembled from recent Git history, outbox messages, task state, and a proof count when available |

[MemoryStore](src/rhea_memory/store.py) opens SQLite in WAL mode. `remember` updates a fact; `recall` retrieves it; `facts` lists them; `forget` removes one. `log` appends an event and `timeline` reads events newest first.

[MemoryFeed](src/rhea_memory/feed.py) examines particular project paths and deduplicates repeated entries. It does **not** take a `MemoryStore` object or automatically summarize that store's facts and timeline. It is a separate view of project activity.

The useful economy is in selection. A project can accumulate more history than the next context window can hold. Decide which observations deserve the crossing, while keeping the original records available for inspection. The generated size/token hints are estimates, not an enforced budget or a promise of lossless compression.

## Install from this checkout

Requires Python 3.10+:

```bash
python3 -m venv .venv
. .venv/bin/activate
python -m pip install -e .
rhea-memory --help
```

The distribution is `rhea-memory`; the Python package is `rhea_memory`. See [pyproject.toml](pyproject.toml).

## Keep a fact; leave a trace

```python
from rhea_memory import MemoryStore, MemoryFeed

store = MemoryStore("./data")  # directory; database is ./data/memory.db
store.remember("project.name", "Rhea")
store.log("Reviewed the next task", {"task": "document the API"})
print(store.recall("project.name"))
print(store.timeline(limit=5))
store.close()

feed = MemoryFeed(project_root=".")
context = feed.generate()
print(context)
```

The equivalent CLI exposes the same basic operations:

```bash
rhea-memory remember project.name Rhea
rhea-memory recall project.name
rhea-memory log "Reviewed the next task"
rhea-memory timeline --limit 5
rhea-memory feed --root .
```

Storage commands use `./data` by default; set `--dir` to choose another location. `feed --root` independently selects the project to inspect. `feed -o <path>` writes the generated text; without `-o`, it prints it. See [the CLI](src/rhea_memory/cli.py) for all arguments.

## What a stored fact means

A value in a database records what was supplied. It does not establish that the value was true. The key/value layer allows replacement and deletion; the timeline appends through its API, but it is not a tamper-proof ledger. This package provides neither distributed consensus nor network replication, and WAL alone does not qualify every crash or power-loss scenario.

The ambition is context you can carry forward without inventing continuity. Маленькая память. Длинное последствие.

## The surrounding system

Start at [the Rhea family entrance](https://blueshoes.space/rhea/).

- [Rhea / Tribunal](https://github.com/timelabs-npo/rhea-project) develops coordination and agent workflows.
- [Rhea CLI](https://github.com/timelabs-npo/rhea-cli) operates Rhea services from a terminal.
- [Omnia Vault](https://github.com/timelabs-npo/omnia-vault) researches state and revision guarantees beyond this small local memory package.
- [Rhea Tutorials](https://github.com/timelabs-npo/rhea-tutorials) includes persistent memory in its planned curriculum.

MIT — see [LICENSE](LICENSE).
