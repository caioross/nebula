# 0004 — Local inference runtime

Status: open — to be decided after the M2 benchmark spike

## Context

Every local model in the fleet ([models.md](../models.md)) runs on an embedded inference engine linked into Nebula's binary. Candidates fall into two families: the llama.cpp lineage reached over FFI (battle-tested kernels, broadest quantization and hardware support, C++ maintenance surface) and Rust-native runtimes such as candle and mistral.rs (cleaner integration, younger kernels, a smaller but sympathetic contributor pool).

## What the decision needs

Numbers, not vibes. The M2 spike benchmarks the shortlist on the actual bundled models across representative hardware (an 8 GB no-GPU laptop, a mid-range machine with modest VRAM, an Apple Silicon machine), measuring tokens per second, time to first token, memory footprint at rest and under load, and cold-load time for the Everyday tier. It also weighs the non-benchmark facts: quantization format coverage, maintenance velocity, and the cost of carrying a C++ dependency versus betting on younger Rust kernels.

## Constraint fixed in advance

Whatever wins is hidden behind the orchestrator's engine interface (tagged-span context in, cancellable token stream out). The rest of Nebula is forbidden from knowing which runtime is underneath — this ADR must stay cheap to revisit, because this corner of the ecosystem improves monthly.
