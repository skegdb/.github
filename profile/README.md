# skegdb

**RAM-frugal KV + vector store for Personal AI inference machines.**

Designed for the box where the LLM owns most of the RAM. SSD-primary
storage, ANN search via Vamana + TurboQuant, multi-tenant isolation,
Redis-compatible wire (RESP3) and a binary protocol.

## Repositories

| repo | what |
|---|---|
| [`skeg`](https://github.com/skegdb/skeg) | engine: vLog, indices, Vamana, server, RESP3 |
| [`skeg-client-rs`](https://github.com/skegdb/skeg-client-rs) | Rust client |
| [`skeg-cli`](https://github.com/skegdb/skeg-cli) | command-line tool |
| [`skeg-tui`](https://github.com/skegdb/skeg-tui) | terminal UI (`skeg-top`) |
| [`skeg-py`](https://github.com/skegdb/skeg-py) | Python SDK |
| [`skeg-llamaindex`](https://github.com/skegdb/skeg-llamaindex) | LlamaIndex vector store |
| [`skeg-ollama`](https://github.com/skegdb/skeg-ollama) | Ollama retriever bridge |
| [`skeg-gleam`](https://github.com/skegdb/skeg-gleam) | Gleam BEAM client |
| [`skeg-testing`](https://github.com/skegdb/skeg-testing) | integration tests + benches |

## Headline benchmark (1M vectors, mxbai 1024-d, recall@10 >= 0.95)

| engine | RSS | p99 | qps |
|---|---|---|---|
| skeg-pq128 | 419 MiB | 3.6 ms | 489 |
| qdrant-hnsw | 4162 MiB | 38.9 ms | 168 |
| chroma-hnsw | 4417 MiB | 9.7 ms | 196 |
| lancedb | 600 MiB | 35.0 ms | 38 (recall 0.64) |

10x less RAM than HNSW engines at higher recall and lower tail. See
[`skeg-testing`](https://github.com/skegdb/skeg-testing) for the full
methodology.

## License

Apache-2.0 for all public repositories. The `skeg-tenant` crate is
distributed under a separate license (decision pending).
