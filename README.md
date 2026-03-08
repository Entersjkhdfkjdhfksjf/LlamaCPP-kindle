# llama.cpp for Kindle PW3

> **Disclaimer:** This README was written with the assistance of Claude.

Running [llama.cpp](https://github.com/ggerganov/llama.cpp) on a jailbroken Kindle Paperwhite 3 (i.MX6SoloLite, ARMv7, 512MB RAM) via a GitHub Actions cross-compile pipeline.

---

## Device Specs

| Property | Value |
|----------|-------|
| Device | Kindle Paperwhite 3 |
| SoC | Freescale i.MX6SoloLite |
| CPU | ARMv7 Cortex-A9 @ ~1GHz (single core) |
| RAM | 512MB (no swap) |
| OS | Stock Kindle OS or Alpine Linux chroot |

---

## How It Works

The Kindle runs a jailbroken version of the stock Kindle OS which has X11, GTK2 and musl libc available. Alpine Linux runs as a chroot on top of this. llama.cpp is cross-compiled on GitHub Actions targeting ARMv7 hard float with musl libc statically linked, producing a single binary with no dependencies that runs on both the stock Kindle OS and Alpine.

---

## Releases

Pre-built binaries and some tested models are available in the [Releases](../../releases) section. If you use any of the provided models, please credit the original authors as listed below in the [Model Credits](#model-credits) section.

---

## Building

The build is fully automated via GitHub Actions. Workflows are available in the Actions tab to build the binary and quantize models.

### Key Build Flags

```
TARGET:    arm-linux-musleabihf
CPU:       cortex-a9
FPU:       vfpv3
FLOAT:     hard
LINKING:   static (no runtime dependencies)
OpenMP:    disabled (single core, caused linking issues)
```

---

## Running on the Kindle

```bash
chmod +x llama-cli
./llama-cli -m your-model.gguf -c 64
```

> **Note:** Keep context size (`-c`) at 64-128. The Kindle has ~250MB free RAM and llama.cpp needs contiguous memory blocks for compute buffers.

---

## Tested Models

> **Note:** Token speeds are approximate and may vary based on model context, system load, temperature, and other external factors.

| Model | Quantization | Size | Tokens/sec | Notes |
|-------|-------------|------|------------|-------|
| stories15M_MOE | Q8_0 | ~30MB | ~15 | Best performance, story generation only |
| stories15M_MOE | FP16 | ~70MB | ~1.5 | Higher quality, much slower |
| SmolLM2-135M-Instruct | Q2_K | ~90MB | ~0.8 | Actual chat, slow but works |
| gpt2-shrek-chaos | Q2_K | ~63MB | ~2 | Genuinely unusable output, not recommended |

---

## Quantizing Models

A separate workflow is included for quantizing models from F16 to smaller formats. Useful for getting models under the ~110MB practical RAM limit. Available in the Actions tab.

---

## Model Credits

- **stories15M_MOE** — [ggml-org](https://huggingface.co/ggml-org/stories15M_MOE)
- **SmolLM2-135M-Instruct** — [HuggingFaceTB](https://huggingface.co/HuggingFaceTB/SmolLM2-135M-Instruct)
- **gpt2-shrek-chaos** — [Amineharrabi](https://github.com/Amineharrabi/shrek_GPT)

---

## Known Limitations

- Single core CPU — no parallelism benefit, OpenMP disabled
- 64-token context recommended — larger contexts eat RAM fast
- Models over ~110MB file size will likely OOM on load

---

## Why

Because it's funny.
