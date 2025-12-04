# Latency Numbers Everyone Should Know (2025)

A quick reference for system design interviews and back-of-the-envelope calculations.

> **Note**: These numbers are approximations for mental math. Real performance varies by hardware, workload, and conditions. Numbers are compiled from multiple sources including vendor specs, academic papers, and industry benchmarks. CPU/cache latencies are well-established; storage numbers reflect typical enterprise NVMe SSDs (PCIe 4.0); memory assumes DDR5.

## CPU & Memory

| Operation | Latency |
|-----------|---------|
| L1 cache reference | ~1 ns |
| Branch mispredict | ~3-5 ns |
| L2 cache reference | ~4 ns |
| L3 cache reference | ~10-20 ns |
| Mutex lock/unlock | ~15-25 ns |
| Main memory reference (DDR5) | ~80-100 ns |
| Sequential memory R/W (64 bytes) | ~0.5 ns |
| Random memory R/W (64 bytes) | ~50 ns |

> **Memory bandwidth (2025)**: DDR5-6400 delivers ~50 GB/s per channel (typically 2-8 channels).

## Hashing & Crypto

| Operation | Latency |
|-----------|---------|
| Non-crypto hash (64 bytes) | 25 ns |
| Crypto-safe hash (64 bytes) | 100 ns |
| Bcrypt a password | 300 ms |

## Operating System

| Operation | Latency |
|-----------|---------|
| System call | 500 ns |
| Context switch | 10 µs |

## Compression

| Operation | Latency/Throughput |
|-----------|---------|
| Compression | ~500 MiB/s |
| Decompression | ~1 GiB/s |

## Storage I/O

| Operation | Latency | Throughput |
|-----------|---------|------------|
| Sequential NVMe SSD read (4KB) | ~10 µs | 5-7 GB/s |
| Sequential NVMe SSD write (4KB, no fsync) | ~10 µs | 4-6 GB/s |
| Sequential NVMe SSD write (4KB, +fsync) | ~1 ms | ~50 MB/s |
| Random NVMe SSD read (4KB) | ~100 µs | ~500 MB/s |
| Random NVMe SSD write (4KB) | ~20 µs | ~300 MB/s |
| Sequential HDD read | ~5 ms | 150-250 MB/s |
| Random HDD read (4KB) | ~10 ms | ~1 MB/s |
| Read 1 MB sequentially from memory | ~20 µs | ~50 GB/s |
| Read 1 MB sequentially from NVMe SSD | ~150-200 µs | 5-7 GB/s |
| Read 1 MB sequentially from HDD | ~5 ms | ~200 MB/s |

> **Note on SSDs**: PCIe 4.0 NVMe SSDs typically achieve 5-7 GB/s sequential reads. PCIe 5.0 can reach 10+ GB/s. SATA SSDs are limited to ~550 MB/s.

## Network

| Operation | Latency |
|-----------|---------|
| TCP echo server (32KB) | 10 µs |
| Proxy (Nginx/HAProxy/Envoy) | 50 µs |
| Network same zone (inside VPC) | 100 µs |
| Network within same region | 250 µs |
| Round trip within same datacenter | 500 µs |
| {Redis, Memcached, MySQL} query | 500 µs |
| Network NA Central ↔ East | 25 ms |
| Network NA Central ↔ West | 40 ms |
| Network NA East ↔ West | 60 ms |
| Network EU West ↔ NA East | 80 ms |
| Network NA West ↔ Singapore | 180 ms |
| Packet CA → Netherlands → CA | 150 ms |

## Blob Storage (S3/GCS)

| Operation | Latency |
|-----------|---------|
| GET (single connection) | 50 ms |
| PUT (single connection) | 50 ms |
| PUT (multipart, n connections) | 150 ms |

## Quick Mental Math

```
1 ns  = 10^-9 seconds
1 µs  = 10^-6 seconds = 1,000 ns
1 ms  = 10^-3 seconds = 1,000 µs = 1,000,000 ns
1 s   = 1,000 ms
```

### Orders of Magnitude

| Level | Time | Example |
|-------|------|---------|
| Nanoseconds (ns) | 1-100 ns | CPU cache, memory reference, hashing |
| Microseconds (µs) | 1-1000 µs | SSD I/O, compression, context switch, same-zone network |
| Milliseconds (ms) | 1-100 ms | Disk seeks, cross-region network, DB queries |
| Hundreds of ms | 100-1000 ms | Cross-continent RTT, bcrypt |

## Key Takeaways

1. **Memory is fast** — L1 cache is ~100x faster than main memory
2. **NVMe changed everything** — Random reads went from ~10ms (HDD) to ~100µs (NVMe)
3. **Network is the bottleneck** — Even same-datacenter RTT is ~500µs
4. **Crypto is expensive** — Bcrypt is intentionally slow (~300ms) for security
5. **Context switches add up** — At ~10µs each, they matter at high throughput
6. **fsync is costly** — SSD writes jump from ~10µs to ~1ms with durability guarantees
7. **Sequential >> Random** — Sequential I/O can be 100x+ faster than random

## References

- [Jeff Dean's original latency numbers](https://gist.github.com/jboner/2841832) — Classic reference (2012 baseline)
- [Systems Performance by Brendan Gregg](https://www.brendangregg.com/systems-performance-2nd-edition-book.html) — Authoritative systems book
- [sirupsen/napkin-math](https://github.com/sirupsen/napkin-math) — Benchmarks and estimation techniques
- [Apple Silicon CPU Optimization Guide](https://developer.apple.com/documentation/apple-silicon/) — Modern CPU latencies
- [NVMe specifications](https://nvmexpress.org/specifications/) — Official NVMe standards
