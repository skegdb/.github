# skegdb

**A vector + key-value store, built the model's way.**

Most vector databases assume they own the machine — every gigabyte the OS can
spare, they take. skegdb assumes the opposite: a box where a language model
already holds the memory. The index lives on the SSD, a small bounded working
set stays in RAM, and the rest is left for the model, the context, and the app.

One engine, a menu of compression tiers, and everything around it — clients,
language integrations, a federation layer, the on-disk format — so a local AI
stack can store and retrieve without renting a second machine for the database.

## The engine — [`skeg`](https://github.com/skegdb/skeg)

mxbai 1024-dim, 100K vectors, recall against exact brute-force cosine, M-series
laptop (serve RSS via `ps`):

| engine | serve RAM | recall@10 | recall@100 | p50 |
| --- | ---: | ---: | ---: | ---: |
| **skeg** (tq2) | **47 MB** | **1.000** | **1.000** | 2.49 ms |
| milvus-lite | 108 MB | 0.934 | 0.880 | 2.69 ms |
| lancedb (IVF-PQ) | 198 MB | 0.998 | 0.991 | 59.3 ms |
| hnswlib | 426 MB | 0.985 | 0.925 | 1.99 ms |
| chroma | 682 MB | 0.985 | 0.919 | 3.91 ms |
| qdrant (f32) | 885 MB | 0.997 | 0.981 | 2.62 ms |

The only engine that is leanest, most accurate, and fast at once — **2–19× less
RAM** than the rest, at the highest recall. The memory that comes back is what
you give to a larger model or a longer context window.

- **The 256 MB test.** In a 256 MB container, skeg serves; Qdrant gets
  OOM-killed. RAM-frugality isn't a nice-to-have on a shared box, it's whether
  the process survives.
- **Multi-tenant density.** One index per tenant, isolated by construction.
  Five tenants of 100K: skeg ~200 MB, Qdrant ~1.8 GB — **9× less**, bounded by
  the largest tenant, not the sum.

How: the Vamana graph is walked on the SSD with a small hot-page cache; vectors
quantize in five tiers (int8, PQ, TurboQuant 1/2/4-bit) and re-rank against
full-precision vectors on disk; an S3-FIFO cache bounded by a byte budget hands
evicted pages back to the OS. Native binary protocol + RESP3.

**Where it loses — straight:** not the fastest single-query engine (Qdrant
matches p99 when RAM isn't contested); younger in production than Qdrant or
Chroma. Reproduce all of it: [`skeg-bench`](https://github.com/skegdb/skeg-bench).

## The ecosystem

| | |
| --- | --- |
| **Engine & format** | |
| [`skeg`](https://github.com/skegdb/skeg) | the KV + vector engine |
| [`skeg-hull`](https://github.com/skegdb/skeg-hull) | the stable, versioned on-disk format |
| [`skeg-kv-cache`](https://github.com/skegdb/skeg-kv-cache) | persistent LLM KV cache — skip the prefill |
| **Clients** | |
| [`skeg-client-rs`](https://github.com/skegdb/skeg-client-rs) | Rust client (binary protocol) |
| [`skeg-py`](https://github.com/skegdb/skeg-py) | Python client |
| [`skeg-gleam`](https://github.com/skegdb/skeg-gleam) | Gleam client |
| [`skeg-cli`](https://github.com/skegdb/skeg-cli) | CLI — offline build, inspect, stats |
| [`skeg-tui`](https://github.com/skegdb/skeg-tui) | `skeg-top`, a terminal UI |
| **AI integrations** | |
| [`skeg-llamaindex`](https://github.com/skegdb/skeg-llamaindex) | LlamaIndex `VectorStore` adapter |
| [`skeg-ollama`](https://github.com/skegdb/skeg-ollama) | Ollama embedding + retrieval pipeline |
| **Federation** | |
| [`hansa`](https://github.com/skegdb/hansa) | federate local AI peers |
| [`skeg-rigging`](https://github.com/skegdb/skeg-rigging) | extension points for memory engines + plugins |
| [`skeg-rigging-net`](https://github.com/skegdb/skeg-rigging-net) | network transports for federation |

## Install

```sh
brew install skegdb/tap/skeg
```

Or `cargo install skeg-server`. Pre-built aarch64 tarballs ship with every
release for Apple Silicon and Linux ARM. Full site + docs:
**[skegdb.github.io](https://skegdb.github.io)**.
