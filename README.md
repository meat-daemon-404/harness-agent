# Harness-Agent (`harness-agent`)

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://github.com/meat-daemon-404/harness-agent)
[![E2E & HTTP Tests](https://img.shields.io/badge/Assertions-32%2F32_Passing-2ea44f)](https://github.com/meat-daemon-404/harness-agent/tree/main/tests)

> **Standalone CodeAct Agent Runtime with Persistent Python Kernel, Append-Only Event Replay & Dual-Mode (`general` / `competition`) Execution** — Zero heavy framework dependencies, `127.0.0.1`-only stdlib WebUI, weighted multi-endpoint LLM failover, and structured `last_error` self-repair.

## Architectural Highlights

1. **Stateful Python Kernel with Structured `last_error` (`lib/kernel.py`)**
   - Maintains a live Python namespace across agent turns (`exec_python`) while automatically injecting `last_error = {"type", "message", "traceback", "lineno", "code"}` on **both** persistent pipe execution and one-shot timeout/fallback paths so the agent can programmatically inspect and repair its own exceptions.
2. **Append-Only Event Ledger & Cost Tracker (`lib/events.py` & `lib/providers.py`)**
   - Every prompt, tool call, stdout/stderr chunk, and file artifact is recorded to `events.jsonl` for deterministic session replay, accompanied by weighted multi-endpoint failover and a real-time `usage.json` token/cost ledger.
3. **Runtime Mode Switch (`general` vs. `competition`)**
   - Switch dynamically between **`general`** software engineering mode and **`competition`** mode (mathematical modelling competition assistant equipped with the 9-section CUMCM/MCM paper skeleton) via CLI (`--mode`), WebUI (`POST /api/mode`), or runtime switching.
4. **Zero-Dependency Localhost WebUI (`lib/webui.py`)**
   - Pure Python standard-library HTTP server bound strictly to `127.0.0.1` (`--port 8790`) providing live event streaming, workspace artifact download, and mode switching.

## Quick Start

```bash
git clone https://github.com/meat-daemon-404/harness-agent.git
cd harness-agent

# 1. Run a task via CLI (general or competition mode)
python run.py --mode general --task "Analyze CSV files in ./data and plot summary statistics"
python run.py --mode competition --task "Build a TOPSIS evaluation model and generate report outline"

# 2. Launch the 127.0.0.1 WebUI console
python lib/webui.py --port 8790

# 3. Run the offline verification suites (18 E2E assertions + 14 HTTP/mode assertions)
python tests/test_e2e_stub.py
python tests/test_mode_http.py
```

## Repository Layout

```text
harness-agent/
├── run.py                    # CLI entrypoint (--task, --mode, --workspace, --max-steps)
├── pyproject.toml            # Package configuration & dependencies
├── lib/
│   ├── agent.py              # CodeAct reasoning & self-repair loop
│   ├── kernel.py             # Persistent Python execution kernel with structured last_error
│   ├── tools.py              # File I/O, shell execution & artifact helpers
│   ├── providers.py          # Multi-endpoint OpenAI-compatible client with failover & usage.json
│   ├── events.py             # Append-only events.jsonl logger & session state reader
│   └── webui.py              # 127.0.0.1 stdlib HTTP server & interactive dashboard
└── tests/
    ├── test_e2e_stub.py      # 18 end-to-end CodeAct & self-repair assertions
    └── test_mode_http.py     # 14 WebUI HTTP API & mode-switching assertions
```

## License

Released under the [MIT License](LICENSE).
