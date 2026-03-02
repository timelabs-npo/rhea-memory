# rhea-memory

Persistent memory layer for AI agents. SQLite-backed key-value store with timeline, compact context generation, and CLI.

## Install

```bash
pip install rhea-memory
```

## Usage

```python
from rhea_memory import MemoryStore, MemoryFeed

store = MemoryStore("memory.db")
store.remember("project.name", "Rhea")
store.log("Deployed v1.0 to production")

feed = MemoryFeed(store)
context = feed.compact()  # ~1500 tokens of compressed context
```

## CLI

```bash
rhea-memory remember key value    # Store a key-value pair
rhea-memory recall key            # Retrieve a value
rhea-memory log "message"         # Append to timeline
rhea-memory timeline              # Show recent events
rhea-memory feed                  # Generate compact context
```

## Why

LLM context windows are expensive. Memory shouldn't be. This gives agents persistent memory across sessions with automatic compaction — SQLite underneath, so it survives restarts, deploys, and deaths.

Part of [TimeLabs NPO](https://github.com/timelabs-npo) open infrastructure.

## License

MIT
