# MODELS

Place local AI model assets in this directory before building or distributing the desktop game.

Recommended contents:

```text
MODELS/
├── qwen2.5-1.5b-instruct-q5_k_m.gguf
├── en_US-lessac-medium.onnx
└── en_US-lessac-medium.onnx.json
```

The desktop launcher selects the first `.gguf` file as the llama.cpp model and the first `.onnx` file as the Piper voice model. Keep the matching Piper `.json` configuration file beside its `.onnx` file.

The models are intentionally external to the executable because they are large binary assets and are often replaced during testing. The portable game executable looks for `MODELS` beside itself first, then inside packaged resources. This permits a one-click launch while still allowing model updates without rebuilding the game shell.

The Qwen model shown in the project setup is an appropriate default. Do not commit large model binaries to source control unless your repository and distribution workflow are prepared for Git LFS or release assets.
