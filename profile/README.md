# skeg

**A RAM-frugal vector + KV store for the box where the model already lives.**

Most vector databases are built for the case where the database owns
the machine. They expect every gigabyte the operating system can spare,
and they take it.

skeg is built for the opposite case: a machine where a language model
already holds most of the memory. The index sits on the SSD, a small
bounded working set stays in RAM, and the rest of the system is left
alone for the model, the context window, the application doing the
actual work.

## The numbers

1M vectors, mxbai 1024-dimensional, recall@10 >= 0.95, on the same
M-series laptop as the model:

| engine | RSS | p99 | qps |
|---|---|---|---|
| **skeg** (pq:128:256) | **419 MiB** | **3.6 ms** | **489** |
| skeg (int8) | 1252 MiB | 10.2 ms | 299 |
| qdrant (hnsw) | 4162 MiB | 38.9 ms | 168 |
| chroma (hnsw) | 4417 MiB | 9.7 ms | 196 |

Ten times less memory than HNSW engines at higher recall and lower
tail latency. The four gigabytes that come back to you are not a
benchmark trick. They are the memory you can give to a larger model, a
longer context window, a second model loaded alongside the first, a
vision encoder, a speech pipeline. Or to nothing, and let the
operating system breathe.

## How it works

The architecture answers one constraint: the resident set of the index
must hold while the model owns the rest of the memory.

- The Vamana graph is walked on the SSD, with a small in-memory cache
  for the hot pages.
- Five tiers of vector quantization (int8, product quantization at
  128 by 256, and TurboQuant at 1, 2, and 4 bits per coordinate) trade
  memory against precision.
- Re-ranking against full-precision vectors held on disk recovers the
  accuracy lost to quantization.
- The cache is S3-FIFO, bounded by a byte budget you configure, and
  gives evicted pages back to the operating system through jemalloc
  decay timers.

The server speaks two protocols. A native binary protocol for clients
that want the lowest overhead. RESP3 for compatibility with existing
Redis tooling.

## Install

```sh
brew tap skegdb/tap
brew install skeg
```

Or `cargo install skeg-server`. Pre-built aarch64 tarballs are
published with every release for Apple Silicon and Linux ARM.

## License

Apache-2.0 for the engine. Production-grade multi-tenant operations
and authentication ship in a separate BUSL-1.1 wrapper for use cases
that need them; single-tenant deployments work out of the box on the
Apache crate.
