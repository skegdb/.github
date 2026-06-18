# skegdb

**A vector + key-value store, built the model's way.**

Most vector databases assume they own the machine and take every gigabyte the OS
can spare. skegdb assumes the opposite: a box where a language model already
holds the memory. The index lives on the SSD, a small bounded working set stays
in RAM, and the rest is left for the model and the app.

One engine with a menu of compression tiers, plus the clients, language
integrations, federation layer, and on-disk format around it, so a local AI
stack can store and retrieve without renting a second machine for the database.

## The engine: [`skeg`](https://github.com/skegdb/skeg)

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

skeg uses 2 to 19x less RAM than the others while matching or beating their
recall, and stays fast. That RAM goes back to the model and its context.

**The 256 MB test.** In a 256 MB container skeg serves; Qdrant gets OOM-killed.
On a shared box, RAM-frugality decides whether the process survives.

**Multi-tenant density.** One index per tenant, isolated by construction. Five
tenants of 100K: skeg around 200 MB against Qdrant's 1.8 GB, 9x less. RAM is
bounded by the largest tenant, not the sum.

How it works: the Vamana graph is walked on the SSD with a small hot-page cache.
Vectors quantize in five tiers (int8, PQ, TurboQuant at 1, 2, and 4 bits) and
re-rank against full-precision vectors on disk. An S3-FIFO cache bounded by a
byte budget hands evicted pages back to the OS. Native binary protocol and RESP3.

**Where it loses.** Not the fastest single-query engine: Qdrant matches p99 when
RAM is not contested. Younger in production than Qdrant or Chroma. Reproduce all
of it with [`skeg-bench`](https://github.com/skegdb/skeg-bench).

## The ecosystem

| | |
| --- | --- |
| **Engine & format** | |
| [`skeg`](https://github.com/skegdb/skeg) | the KV + vector engine |
| [`skeg-hull`](https://github.com/skegdb/skeg-hull) | the stable, versioned on-disk format |
| **Clients** | |
| [`skeg-client-rs`](https://github.com/skegdb/skeg-client-rs) | Rust client (binary protocol) |
| [`skeg-py`](https://github.com/skegdb/skeg-py) | Python client |
| [`skeg-cli`](https://github.com/skegdb/skeg-cli) | CLI: offline build, inspect, stats |
| **AI integrations** | |
| [`skeg-llamaindex`](https://github.com/skegdb/skeg-llamaindex) | LlamaIndex `VectorStore` adapter |
| [`skeg-ollama`](https://github.com/skegdb/skeg-ollama) | Ollama embedding + retrieval pipeline |
| **Federation** | |
| [`hansa`](https://github.com/skegdb/hansa) | federate local AI peers |
| [`skeg-rigging`](https://github.com/skegdb/skeg-rigging) | extension points for memory engines and plugins |
| [`skeg-rigging-net`](https://github.com/skegdb/skeg-rigging-net) | network transports for federation |

## Install

```sh
brew install skegdb/tap/skeg
```

Or `cargo install skeg-server`. Pre-built aarch64 tarballs ship with every
release for Apple Silicon and Linux ARM. Docs live in the
[engine repo](https://github.com/skegdb/skeg).
