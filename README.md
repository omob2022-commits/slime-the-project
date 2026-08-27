# Slime Kingdom Clicker - Portable Desktop Build

![Slime Kingdom Clicker](https://img.shields.io/badge/Platform-Windows%20Portable-blue) ![License](https://img.shields.io/badge/Status-Standalone%20Desktop-green)

**Slime Kingdom Clicker** is an interactive clicker game featuring a integrated local AI companion sidecar. This repository contains the standalone portable desktop application build.

---

## 🌟 Key Features

- **Standalone Portable Executable**: Runs as a single portable `.exe` on Windows with embedded Electron shell and Vite frontend.
- **Local AI Companion**: Integrated sidecar running local LLM inference (via `llama-server`) and text-to-speech voice synthesis (via `piper`).
- **Flexible Model Loading**: Easily swap AI models by updating files in the local `MODELS/` folder without rebuilding the game application.

---

## 🤖 Swapping & Customizing AI Models

You can change the AI model to **whatever GGUF model you want**!

### How it works:
1. Open the `MODELS/` directory located alongside `Slime-Kingdom-Clicker.exe`.
2. Place your desired GGUF format model file (e.g., any `*.gguf` model such as Qwen2.5, Llama-3, Mistral, etc.) inside the `MODELS/` directory.
3. The desktop application automatically detects and loads the **first `.gguf` file** found in the `MODELS/` folder upon startup.

```text
Slime-Kingdom-Clicker.exe
MODELS/
├── your-custom-model.gguf      <-- Place any GGUF LLM model here!
├── en_US-lessac-medium.onnx     <-- Piper TTS Voice Model (optional)
└── en_US-lessac-medium.onnx.json
```

> **Note**: Because GGUF models are large binary files (>1 GB), model binaries are kept separate from Git source control. Simply download or drop your preferred `.gguf` file into `MODELS/`.

---

## 🚀 Getting Started

1. Download or clone this repository.
2. Ensure your model file (`.gguf`) is placed inside the `MODELS/` folder beside the executable.
3. Double-click `Slime-Kingdom-Clicker.exe` to launch the game!

---

## 🛠️ Build & Architecture

For detailed build instructions, developer setup, Python sidecar compilation, and cross-platform details, refer to [DESKTOP_BUILD.md](file:///home/omob/Projects/slime-kingdom-clicker-windows-portable/DESKTOP_BUILD.md).
