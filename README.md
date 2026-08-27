# Slime Kingdom Clicker

Slime Kingdom Clicker is a standalone desktop clicker game with an integrated local AI companion. The companion runs entirely offline using local LLM inference and text-to-speech synthesis, requiring no internet connection or external API keys.

This repository contains the portable Windows build.

## Features

- **Portable executable** — single `.exe` launch with no installation required.
- **Local AI companion** — LLM inference via `llama-server` and voice synthesis via `piper`, running entirely on your machine.
- **Swappable models** — drop any compatible model into the `MODELS/` folder to change the AI behaviour without rebuilding the application.

## Customising the AI Model

The game loads the first `.gguf` file it finds in the `MODELS/` directory at startup. You can replace the bundled model with any GGUF-compatible language model of your choice (Qwen, Llama, Mistral, Phi, etc.).

To swap models:

1. Open the `MODELS/` folder located beside `Slime-Kingdom-Clicker.exe`.
2. Remove or rename the existing `.gguf` file.
3. Place your preferred `.gguf` model file in the same folder.
4. Launch the game — the new model will be loaded automatically.

```
Slime-Kingdom-Clicker.exe
MODELS/
  your-model.gguf
  en_US-lessac-medium.onnx          (optional — Piper TTS voice)
  en_US-lessac-medium.onnx.json
```

Model binaries are large and are not tracked by Git. Download your preferred model separately and place it in `MODELS/` before running the game.

## Getting Started

1. Clone or download this repository.
2. Place a `.gguf` model file in the `MODELS/` folder.
3. Run `Slime-Kingdom-Clicker.exe`.

## Build Instructions

For full build and development details — including cross-platform packaging, sidecar compilation, and the runtime startup sequence — see [DESKTOP_BUILD.md](DESKTOP_BUILD.md).
