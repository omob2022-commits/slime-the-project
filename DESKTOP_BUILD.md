# Standalone desktop build

The desktop build uses Electron as a thin game shell. It packages the compiled Vite game, the existing AI companion frontend, the Python sidecar compiled as a native helper, and the `MODELS` directory into a portable application. When the game starts, `desktop/main.cjs` launches `llama-server`, the companion sidecar, and then the local game window. When the window closes, Electron terminates the child processes.

## What “single executable” means here

On Windows, `pnpm desktop:package:win` produces a portable `Slime-Kingdom-Clicker-1.0.0-portable.exe` in `release/`. The packaged executable contains the Electron shell, compiled game, sidecar executable, and any model files present in `MODELS` at build time. The player can launch that one `.exe`.

For practical model updates, the launcher also checks for a sibling `MODELS` folder next to the executable before using the packaged copy. This means you may ship either one very large portable executable or a smaller executable accompanied by:

```text
Slime-Kingdom-Clicker.exe
MODELS/
  qwen2.5-1.5b-instruct-q5_k_m.gguf
  en_US-lessac-medium.onnx
  en_US-lessac-medium.onnx.json
```

The second layout is usually better for Steam patches because model files can be updated without rebuilding the Electron shell. It still has one-click execution: the user launches only the `.exe`.

## Windows build requirements

Build the Windows artifact on Windows or in a CI environment capable of producing Windows Electron artifacts. Install Node.js, pnpm, Python, PyInstaller, a CUDA-enabled `llama-server.exe`, and `piper.exe`.

From the project root:

```powershell
pnpm install
python -m pip install pyinstaller
```

Copy the Qwen model and Piper voice model into `MODELS/`. Copy the native runtime executables into `runtime/bin/`:

```text
runtime/bin/
  llama-server.exe
  piper.exe
```

Then build the sidecar and portable game:

```powershell
.\scripts\build-desktop.ps1
```

The script stages `companion_sidecar.exe` into `runtime/bin/`, checks that a `.gguf` and `.onnx` model exist, runs the frontend/production build, and invokes electron-builder’s Windows portable target.

To run the packaging steps separately:

```powershell
.\scripts\build-sidecar.ps1
pnpm check
pnpm build
pnpm desktop:package:win
```

## Linux and macOS

Use the Unix script on Linux or macOS:

```bash
chmod +x scripts/*.sh
./scripts/build-desktop.sh
```

The generic desktop command produces the configured platform artifact for the current build host. Explicit commands are:

```bash
pnpm desktop:package:linux
pnpm desktop:package:mac
```

Native `llama-server` and Piper binaries must match the target operating system and CPU architecture. Do not copy Windows binaries into a Linux or macOS package.

## Development launch

For a local development run after building the web assets:

```bash
pnpm desktop:dev
```

This loads the bundled production web output inside Electron and starts the local AI processes. To use a running Vite dev server instead:

```bash
pnpm dev
pnpm desktop:dev:vite
```

The development launcher can fall back to `python3 sidecar/companion_sidecar.py` when the PyInstaller sidecar has not yet been staged. The packaged executable requires `runtime/bin/companion_sidecar.exe` or its platform equivalent.

## Runtime startup sequence

The main process performs these steps:

| Step | Operation |
| --- | --- |
| 1 | Resolve the executable/resource root and find `MODELS`. |
| 2 | Select the first `.gguf` file as the Qwen model. |
| 3 | Start `llama-server` on `127.0.0.1:8080` with CUDA layer offload. |
| 4 | Start the compiled companion sidecar on `127.0.0.1:39281`. |
| 5 | Wait briefly for the sidecar health endpoint. |
| 6 | Open the compiled game in a sandboxed Electron `BrowserWindow`. |
| 7 | Stop llama.cpp and the sidecar when the game closes. |

If the model or a native binary is absent, the game still opens. The companion remains available in authored captions-only fallback mode, but full local AI and voice require the corresponding files.

## Hardware tuning

The launcher defaults to a `2048` llama.cpp context and `99` GPU layers. Override them when launching from a terminal or by editing the environment used by the launcher:

```text
LLAMA_CONTEXT=1024
LLAMA_GPU_LAYERS=35
```

A GTX 2060-class card can generally start with full layer offload for a 1.5B Q5 model. A GTX 1060 with 3 GB VRAM may need a shorter context or fewer GPU layers. The companion’s prompts are intentionally short, so a large context is unnecessary.

## Verification

Run these checks before publishing:

```bash
pnpm check
pnpm build
python3 -m py_compile sidecar/companion_sidecar.py
```

Then launch the packaged application with no terminal attached. Confirm that the game window appears, the model processes are visible in the platform process list, the companion can answer a question, voice playback starts after the first interaction, and closing the game terminates both local AI processes.
