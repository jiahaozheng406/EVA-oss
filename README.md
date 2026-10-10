# EVA

### Embodied Vision-Language-Action Robot System

[**Live Demo**](https://jiahaozheng406.github.io/EVA-oss/) · [**Jiahao Zheng**](https://jiahaozheng406.github.io)

**EVA** is a collaborative embodied-AI project by **[Jiahao Zheng](https://jiahaozheng406.github.io)** and **[Liyao Wu](https://github.com/xxraincandyxx)**, exploring **vision-language-action (VLA) models, robotic manipulation, perception, motion control, and efficient edge deployment** for six-DoF robotic systems.

This repository is **Jiahao Zheng's research fork and development workspace** for EVA. It preserves the original project history and implementation while serving as a basis for continued research on **3D spatial perception, embodied intelligence, and Real2Sim/Sim2Real robotic learning**.

> **Research focus:** Embodied AI · Vision-Language-Action · Robotic Manipulation · 3D Vision · Spatial Intelligence · Edge AI

## Project overview

EVA connects high-level visual/language reasoning with low-level robot execution. The current system combines:

- **Python orchestration and perception** for application logic, vision, model inference, and device scheduling.
- **C++ motion control and kinematics** exposed to Python through `pybind11`.
- **React + Three.js operator interface** for visualization and interactive robot operation.
- **Edge-oriented deployment support** for running perception and control components close to the robot.

The overall runtime flow is:

```text
Vision / Language Input
        ↓
Perception & Model Inference
        ↓
Python Orchestration Layer
        ↓
pybind11 Interface
        ↓
C++ Kinematics & Motion Control
        ↓
Six-DoF Robot
```

## Research directions

This fork is intended to support continued work around EVA, with particular interest in:

- **Vision-Language-Action models** for instruction-conditioned robotic manipulation.
- **3D-aware perception and spatial reasoning** for more robust interaction with real environments.
- **Semantic 3D representations** that connect object identity, geometry, pose, and spatial relationships.
- **Real2Sim / Sim2Real learning** for transferring policies between reconstructed simulation environments and physical robots.
- **Efficient embodied models and edge inference** for practical real-time deployment.

These items describe the ongoing research direction of this fork; not all are implemented in the current codebase yet.

## Collaboration

EVA is developed as a collaborative project between:

- **Jiahao Zheng** — 3D vision, spatial intelligence, Real2Sim/Sim2Real, embodied perception and robotic learning  
  Homepage: <https://jiahaozheng406.github.io>
- **Liyao Wu** — VLA models, reinforcement learning, robotic systems and efficient model inference  
  GitHub: <https://github.com/xxraincandyxx>

The upstream open-source repository is maintained at [`xxraincandyxx/EVA-oss`](https://github.com/xxraincandyxx/EVA-oss). This fork keeps the upstream attribution and commit history while hosting Jiahao Zheng's ongoing research extensions.

## Repository layout

```text
dynamo_/               C++ kinematics and device-control library
src/backend/            Python application, web API, vision, and model code
src/frontend/           React operator interface
stubs/                  Type information for the native Python module
tests/                  Deterministic Python unit tests
experiments/            Reproducible training reports
requirements/           Runtime, ML, development, and edge dependency sets
docs/                   Architecture and deployment boundaries
```

See [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for component ownership, runtime flow, and persistent-data locations.

Runtime data, credentials, model weights, captured images, build products, and local databases are intentionally excluded from version control.

## Development setup

Python 3.9+, CMake 3.20+, a C++17 compiler, and Node.js 20.19+ or 22.12+ are required.

```bash
python3 -m venv .venv
.venv/bin/pip install -r requirements/dev.txt
.venv/bin/pip install --no-deps -e .
npm --prefix src/frontend ci
PYTHON=.venv/bin/python ./build.sh
```

For a minimal KV260/Vitis AI installation, use `requirements/edge.txt` instead of the development requirements. The Vitis `xir` and `vart` packages must be available from the board's configured package source.

## Run

Start the services in separate terminals:

```bash
.venv/bin/python -m backend
VITE_EVA_BACKEND_URL=http://127.0.0.1:8080 npm --prefix src/frontend run dev
```

The backend listens on port `8080` by default and the Vite development server uses port `5173`. A production frontend served by the backend uses the current origin and does not need `VITE_EVA_BACKEND_URL`. Hardware devices, model paths, and remote LLM credentials are deployment configuration and must not be committed to the repository.

Run `.venv/bin/python -m backend --help` for device, camera, vision, TLS, and network options. Every option also has an `EVA_*` environment variable for service deployments.

## Checks

```bash
make lint          # Python and frontend static checks
make format-check  # Python and C++ formatting checks
make test          # Python, C++, and frontend build checks
make check         # Complete verification suite
```

The C++ suite can be built without Python bindings when only the control core is needed:

```bash
cmake -S . -B build -DEVA_BUILD_PYTHON=OFF
cmake --build build --parallel
ctest --test-dir build --output-on-failure
```

## Security

Supply secrets through environment variables or a deployment secret manager. Do not commit TLS keys, API tokens, device credentials, or generated databases.

## GitHub Pages

The `deploy-pages.yml` workflow publishes the React interface from `master`. GitHub Pages is static hosting: robot control, camera streams, Socket.IO, and agent requests remain offline unless the frontend is built with `VITE_EVA_BACKEND_URL` pointing to a separately deployed HTTPS backend.

## License

This project is released under the [MIT License](LICENSE). The original copyright and license notice are preserved.
