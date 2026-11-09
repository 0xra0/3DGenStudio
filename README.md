<div align="center">

# 🌌 3D Gen Studio

**The Open-Source AI-Powered 3D Mesh Production Studio & Pipeline Orchestrator**

[![GitHub Release](https://img.shields.io/github/v/release/0xra0/3DGenStudio?style=flat-square&color=8A2BE2)](https://github.com/0xra0/3DGenStudio/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)
[![Node.js](https://img.shields.io/badge/Node.js-18%2B%20%7C%2020%2B-green.svg?style=flat-square&logo=node.js)](https://nodejs.org)
[![Python](https://img.shields.io/badge/Python-3.10%20--%203.13-blue.svg?style=flat-square&logo=python)](https://python.org)
[![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20Windows%20%7C%20macOS%20(Apple%20Silicon)-orange.svg?style=flat-square)]()
[![Model Context Protocol](https://img.shields.io/badge/MCP-Supported-purple.svg?style=flat-square)](https://modelcontextprotocol.io)

*Orchestrate end-to-end 3D generative pipelines — from text-to-image and multi-view generation to 3D mesh reconstruction, UV unwrapping, PBR texturing, neural auto-rigging, animation retargeting, and game engine export — all inside a unified visual workspace powered by ComfyUI, Three.js, and local AI microservices.*

[**Features**](#-features) • [**Architecture**](#-architecture) • [**Quick Start**](#-quick-start) • [**Installation**](#-installation-guide) • [**ComfyUI Setup**](#-comfyui-integration) • [**MCP Server**](#-model-context-protocol-mcp) • [**Docs**](#-documentation)

---

</div>

## 📖 Overview

**3D Gen Studio** bridges the gap between state-of-the-art generative 2D/3D AI models and production game engines (Unreal Engine, Unity, Godot, Blender). Rather than juggling disparate Python scripts, command-line tools, and web services, 3D Gen Studio unifies the entire workflow into a local-first visual operating system.

### Why 3D Gen Studio?
- 🔒 **Local-First & Private:** Everything runs on your own hardware or private network. Your assets, prompts, models, and databases stay strictly under your control.
- 🔄 **Flexible Production Views:** Seamlessly switch between an intuitive **Kanban board** for asset lifecycle tracking and an expressive **Node Graph** for complex pipeline composition.
- 🛠️ **Full Mesh Post-Processing Suite:** Built-in sculpting, brush painting, automatic UV parameterization, CUDA-accelerated retopology, normal/AO baking, and game-ready budget validation.
- 🦴 **AI Auto-Rigging & Motion:** Neural skeleton estimation via SkinTokens, video-to-motion MoCap via MoCapAnything, and text-to-motion generation via Kimodo (Meta Llama 3).
- 🏗️ **Procedural Generators:** Create multi-storey architectural buildings from 2D floorplans, algorithmic trees with wind shader vertex attributes, and particle VFX with cross-engine export.
- 🤖 **Model Context Protocol (MCP) Native:** Connect Claude Desktop, Cursor, or autonomous LLM agents directly to your studio to inspect assets, run ComfyUI workflows, and execute batch pipelines programmatically.

---

## 🏗️ Architecture

```mermaid
flowchart TD
    subgraph Client["🖥️ Frontend & Desktop"]
        UI["React 19 + Vite + Tailwind"]
        Three["Three.js / React Three Fiber Viewport"]
        Electron["Electron Shell (Cross-Platform Desktop)"]
    end

    subgraph Backend["⚙️ Studio Core Backend (Node.js Express)"]
        Server["Express API & WebSocket Gateway :3001"]
        DB[("SQLite (Local) / PostgreSQL (Multi-User)")]
        MCPServer["MCP Server (stdio / HTTP)"]
    end

    subgraph Services["🐍 Specialized AI & Mesh Microservices"]
        PyServer["Python Mesh-Tools (FastAPI/uv) :8000\n(AutoUV, AutoRetopo, Baking, FBX, Repair)"]
        SkinTokens["SkinTokens Rigging Server :8001\n(Neural Skeleton & Weight Estimation)"]
        MoCap["MoCapAnything Server :8002\n(Video-to-Motion Tracking)"]
        Kimodo["Kimodo Motion Server :8003\n(Text-to-Motion via LLM2Vec / Llama 3)"]
    end

    subgraph Generative["🎨 Generative AI Engines"]
        ComfyUI["Local / Remote ComfyUI Instance\n(Trellis 2, Pixal3D, Hunyuan3D, UltraTex, Flux)"]
        ExtAPI["External AI APIs (Tripo3D, Tencent Cloud, Fal.ai)"]
    end

    subgraph Engines["🎮 Production Target Engines"]
        Unreal["Unreal Engine (GLB / FBX / PBR / Collision)"]
        Unity["Unity (Mesh + Rig + VFX Graph Export)"]
        Blender["Blender (.blend / FBX / OBJ)"]
    end

    UI <--> Server
    Electron --> UI
    Server <--> DB
    MCPServer <--> Server
    Server <--> PyServer
    Server <--> SkinTokens
    Server <--> MoCap
    Server <--> Kimodo
    Server <--> ComfyUI
    Server <--> ExtAPI
    PyServer --> Engines
```

---

## ✨ Features

### 📋 Kanban Production Pipeline
Organize assets through standardized stages: `Images` → `Image Edit` → `Mesh Gen` → `Mesh Edit` → `Texturing` → `Export`.
- **Drag-and-Drop Workflow:** Move assets across lifecycle phases with live status updates.
- **In-Card Asset Carousel:** Browse multi-angle renders, wireframes, and historical version variants.
- **Inline Action Triggers:** Launch ComfyUI nodes or external APIs directly from a card.
- **Custom Tags & Metadata:** Categorize assets by project, style, polygon budget, and target engine.

### 🕸️ Visual Node Graph
Assemble complex, multi-branch pipelines visually using an intuitive node canvas powered by `@xyflow/react`.
- Connect image generation nodes to background removers, normal estimators, and 3D mesh generators.
- Expose ComfyUI workflow parameters directly inside node inspectors.
- Chain outputs automatically: image outputs feed mesh generators, which feed texture projectors, which feed LOD bakers.
- Full undo/redo, auto-layout reorganization, and persistent zoom/pan states.

### 🎨 3D Mesh Editor & Sculpting Studio
An integrated Three.js 3D viewport tailored for direct asset inspection and modification:
- **PBR Viewport:** Real-time physically based rendering (Roughness, Metallic, Normal, AO, Emissive) with environment HDR maps, orthographic/perspective toggles, and wireframe overlays.
- **Sculpting & Modeling:** Sculpt meshes using brushes (Standard, Clay, Inflate, Smooth, Flatten, Pinch, Grab) and vertex displacement with tessellation.
- **Texture Projection & Inpainting:** Project ComfyUI inpainting results onto selected UV islands with seam-smoothing shaders.
- **UltraTex Integration:** Multi-view neural texturing for ultra-crisp 4K PBR surface detail.
- **Automatic UV Unwrapping (Auto UV):** Clean, non-overlapping UV atlas generation powered by xatlas.
- **Automatic Retopology (Auto Retopo):** CUDA-accelerated remeshing for quad/tri optimization, non-manifold edge removal, and open boundary repair.
- **Texture Baking:** High-to-low poly baking of Normal (DirectX & OpenGL), Ambient Occlusion, Tangent, and Position maps.
- **Optimization & LOD Chains:** Generate multi-resolution LODs (LOD0 through LOD3) and convex collision hulls via `gltfpack` and `meshoptimizer`.
- **Game-Ready Engine Auditor:** Automated validation against target engine budgets (triangle count, draw calls, UV coverage, non-manifold geometry, transform scale, and pivot positioning).

### 🦴 AI Auto-Rigging & Animation Studio
- **Neural Skeleton Generation:** Powered by **SkinTokens**, automatically infer humanoid/creature skeletons and skinning weights from arbitrary 3D geometry without manual bone placement.
- **Skeleton & Weight Editing:** Adjust bone positions, rename joints, edit vertex weights, or transfer existing armatures from high-poly reference meshes to optimized low-poly meshes.
- **Video-to-Motion (MoCapAnything):** Extract 3D skeletal motion from monocular reference video clips.
- **Text-to-Motion (Kimodo):** Synthesize animations directly from natural language prompts using LLM2Vec and Meta Llama 3 embeddings.
- **Animation Retargeting:** Retarget animations onto custom humanoid skeletons and export as standard FBX or animated GLTF/GLB.

### 🏛️ Procedural Building Generator
- Draw 2D architectural footprints and wall layouts in a vector canvas.
- Automatically extrude and construct multi-storey buildings with parametric floors, roof styles (gable, hip, flat, stacked), trim profiles, and slot meshes for doors and windows.
- Export parametric style packs and Intermediate Representation (IR) graphs.

### 🌳 Procedural Tree Generator
- Algorithmic plant generation with configurable branching density, trunk taper, crown radius, and leaf particle distributions.
- Built-in vertex color baking for wind simulation shaders in Unreal and Unity.

### ✨ Procedural VFX Generator
- Visual node graph for particle emitters, turbulence fields, burst dynamics, and material shaders.
- Cross-engine export bundles with automated Intermediate Representation (IR) mapping for Unity (VFX Graph / Shuriken) and Unreal Engine (Niagara).

### 🧩 Assembly Editor
- Compose complex multi-part props, vehicles, and modular kits.
- Parametric snapping, bounding box alignment, and hierarchical scene tree management.

### ⚡ Batch Project Runner
- Execute batch pipelines across hundreds of assets unattended.
- Automate multi-step pipelines: ComfyUI image-to-mesh → Auto Retopo → Rig Transfer → Normal/AO Bake → Flatten to Albedo → Multi-LOD Export.

### 📚 Centralized Assets Library
- Unified search, tagging, and version control for images, meshes (GLB, OBJ, FBX), textures, and ComfyUI workflow JSONs.
- Instant 3D thumbnail generation and one-click import into active projects.

---

## 🤖 Model Context Protocol (MCP)

3D Gen Studio exposes a full **Model Context Protocol (MCP)** server (`npm run mcp:stdio`), enabling LLM agents (Claude Desktop, Cursor, ChatGPT, Antigravity) to act as autonomous technical artists.

### Adding to Claude Desktop

Add the following to your Claude Desktop configuration (`~/.config/Claude/claude_desktop_config.json` on Linux/macOS or `%APPDATA%\Claude\claude_desktop_config.json` on Windows):

```json
{
  "mcpServers": {
    "3dgenstudio": {
      "command": "node",
      "args": ["/path/to/3DGenStudio/mcp/stdio.js"]
    }
  }
}
```

### Key MCP Capabilities
- `list_projects` / `get_project`: Query projects, cards, and canvas nodes.
- `create_asset` / `import_mesh`: Ingest images, 3D meshes, and animations.
- `run_comfy_workflow`: Trigger ComfyUI generation pipelines with dynamic input overrides.
- `execute_batch`: Run automated batch processing across asset collections.
- `inspect_mesh`: Query vertex counts, manifoldness, UV layout, and game-engine readiness budgets.
- `apply_retopo` / `apply_autorig`: Trigger remote Python processing tools programmatically.

---

## 💻 Tech Stack

| Domain | Technologies & Libraries |
| :--- | :--- |
| **Frontend UI** | React 19, Vite 8, React Router 7, TailwindCSS, `@xyflow/react`, Excalidraw |
| **3D Engine** | Three.js, `@react-three/fiber`, `@react-three/drei`, `three-bvh-csg`, `three-mesh-bvh` |
| **Desktop Shell** | Electron 43, `electron-builder` |
| **Backend Core** | Node.js (ESM), Express 5, WebSocket (`ws`), Multer |
| **Data & Storage** | SQLite3 (Desktop / Single-User), PostgreSQL (`pg`, Multi-User Server), LowDB |
| **Python Mesh Tools** | Python 3.10–3.13, FastAPI, `uv`, Trimesh, PyVista, xatlas, Blender `bpy`, NVIDIA Warp, CuPy |
| **Neural AI Services** | ComfyUI, SkinTokens (Auto-Rigging), MoCapAnything (Video MoCap), Kimodo (Llama 3 Text-to-Motion) |
| **Mesh Optimization** | `meshoptimizer`, `gltfpack` |
| **AI Protocol** | Model Context Protocol (`@modelcontextprotocol/sdk`) |

---

## ⚡ Quick Start

### Prerequisites
- **Node.js**: `18.x`, `20.x`, or higher ([Download Node.js](https://nodejs.org))
- **Python**: `3.10` to `3.13` (Astral [`uv`](https://astral.sh/uv) will auto-provision Python if not installed)
- **Git**: With Git LFS installed (`git lfs install`)
- **GPU (Optional, Recommended)**: NVIDIA GPU with CUDA 11.8+ or 12.x for accelerated neural retopo and rigging. CPU fallback is fully supported.

### One-Click Launch

#### Linux / macOS:
```bash
git clone https://github.com/0xra0/3DGenStudio.git
cd 3DGenStudio
chmod +x run.sh
./run.sh
```

#### Windows:
```cmd
git clone https://github.com/0xra0/3DGenStudio.git
cd 3DGenStudio
run.bat
```

> **Note:** On first startup, `run.sh` / `run.bat` installs Node dependencies, provisions the Python environment via `uv`, downloads necessary wheels, and launches the application at `http://localhost:5173`.

---

## 🛠️ Installation Guide

If you prefer to configure each subsystem individually:

### 1. Core Web Application (Node.js + Vite)

```bash
# Clone the repository
git clone https://github.com/0xra0/3DGenStudio.git
cd 3DGenStudio

# Install JavaScript dependencies
npm install

# Start both Node backend (port 3001) and Vite frontend (port 5173)
npm run dev
```

### 2. Python Mesh-Tools Service

The Python service handles heavy geometric computations (UV unwrapping, retopology, repair, baking, FBX export, thumbnail generation).

```bash
cd python-server

# On Linux/macOS using run.sh (automatically installs uv, python 3.13 venv, and CUDA cupy):
bash run.sh

# Or manually:
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -r requirements.txt

# For NVIDIA GPU acceleration (Auto Retopo & Baking):
pip install -r requirements-nvidia.txt
python detect_cuda.py  # installs matching cupy-cudaXXx package

python main.py
```
*The mesh-tools service runs at `http://localhost:8000` (configurable via `MESHTOOLS_PORT`).*

### 3. Optional AI Microservices

#### A. SkinTokens (Neural Auto-Rigging)
```bash
cd thirdparty/skintokens
bash run_server.sh  # Windows: run_server.bat
```
*Runs on `http://localhost:8001`. Automatically downloads pretrained skeleton & skin weight prediction checkpoints.*

#### B. MoCapAnything (Video-to-Motion)
```bash
cd thirdparty/mocapanything
bash run_server.sh  # Windows: run_server.bat
```
*Runs on `http://localhost:8002`. Allows uploading MP4/MOV videos to extract 3D joint motion.*

#### C. Kimodo (Text-to-Motion with Meta Llama 3)
```bash
cd thirdparty/kimodo
bash run_server.sh
```
*Requires accepting the [Meta Llama 3 Community License](META-LLAMA-3-LICENSE) in Studio Settings.*

---

## 🎨 ComfyUI Integration

3D Gen Studio seamlessly connects to local or networked ComfyUI instances.

1. **Start ComfyUI:** Ensure your ComfyUI server is running (default: `http://127.0.0.1:8188`).
2. **Configure Studio:** In 3D Gen Studio, navigate to **Settings → ComfyUI** and set your ComfyUI host and port.
3. **Pre-Built Workflows:** Explore the included workflows under `comfyui_workflows/`:
   - `Trellis2_MeshGen.json` — Fast multi-view to 3D mesh synthesis
   - `Pixal3D_Reconstruction.json` — Dense PBR mesh generation
   - `Hunyuan3D_2.1.json` — High-fidelity image-to-3D
   - `UltraTex_PBR_Paint.json` — High-resolution texture painting & projection
   - `Marigold_Normal_Albedo.json` — Normal map & diffuse estimation
   - `Flux_Inpainting.json` — Seam healing and text-guided texture touch-up

---

## 🐳 Docker & Multi-User Server Deployment

For studio teams or NAS hosting with PostgreSQL:

```bash
# Copy and configure the environment template
cp .env.docker.example .env

# Edit database credentials and JWT secret
nano .env

# Launch with Docker Compose
docker-compose up -d
```

The Docker stack boots:
- `genstudio-server`: The Node.js Express backend and compiled UI on port `3001`
- `genstudio-postgres`: PostgreSQL database with automated migrations
- Persistent asset storage mounted under `./data/assets`

### Reverse Proxy Configuration (Nginx / Caddy)
When hosting behind Nginx or Traefik, forward client host headers so generated asset URLs resolve properly:

```nginx
server {
    listen 80;
    server_name studio.yourdomain.com;

    location / {
        proxy_pass http://127.0.0.1:3001;
        proxy_set_header Host              $http_host;
        proxy_set_header X-Forwarded-Host  $http_host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Port  $server_port;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```
Or override the public URL explicitly:
```bash
PUBLIC_BASE_URL=https://studio.yourdomain.com npm start
```

---

## 📦 Desktop Application Packaging

You can package 3D Gen Studio as a standalone desktop executable for offline usage:

```bash
# Package for your current operating system
npm run dist

# Target-specific platform builds:
npm run dist:linux    # Generates .AppImage / .deb in release/
npm run dist:win      # Generates NSIS installer (.exe) in release/
npm run dist:mac      # Generates Apple Silicon .dmg in release/
```

> **macOS Note:** Builds are Apple Silicon only (M1+). Because Blender `bpy` no longer supports macOS x86_64, Intel Macs are not supported. If Gatekeeper alerts on first launch, run:
> ```bash
> xattr -dr com.apple.quarantine "/Applications/3D Gen Studio.app"
> ```

---

## ⚙️ Configuration Reference

Key environment variables configurable in `.env` or system environment:

| Variable | Default | Description |
| :--- | :--- | :--- |
| `PORT` | `3001` | Express backend server port |
| `MESHTOOLS_PORT` | `8000` | Python mesh-tools service port |
| `SKINTOKENS_PORT` | `8001` | SkinTokens auto-rig service port |
| `MOCAP_PORT` | `8002` | MoCapAnything video-to-motion port |
| `COMFYUI_URL` | `http://127.0.0.1:8188` | ComfyUI server address |
| `PUBLIC_BASE_URL` | *Unset* | Explicit public URL when running behind NAT/reverse proxy |
| `TRUST_PROXY_HEADERS` | `1` | Parse `X-Forwarded-*` headers (`0` to disable) |
| `DATABASE_URL` | *Unset (SQLite)* | PostgreSQL connection string (`postgresql://user:pass@host:5432/db`) |
| `JWT_SECRET` | *Auto-generated* | Secret key for multi-user authentication tokens |

---

## 📚 Documentation & Guides

Comprehensive guides are located in the `docs/` folder:
- [ComfyUI Integration Guide](docs/ComfyUI.md) & [Managed ComfyUI Setup](docs/COMFYUI_MANAGED.md)
- [Building Graph Schema & IR Specification](docs/BUILDING_GRAPH_SCHEMA.md)
- [VFX Engine Mapping & Unity/Unreal Export](docs/VFX_ENGINE_MAPPING.md)
- [Model Context Protocol (MCP) Guide](docs/mcp.md)
- [Desktop Packaging & Build Guide](docs/DESKTOP_BUILD.md)
- [Dependency & Override Notes](docs/DEPENDENCIES.md)

---

## ⚖️ Licensing & Attributions

- **Core Application:** Released under the [MIT License](LICENSE).
- **Kimodo Motion Service:** Powered by [Kimodo](https://github.com/nv-tlabs/kimodo) (Apache 2.0) and [LLM2Vec](https://github.com/McGill-NLP/llm2vec) using Meta Llama 3 weights under the [Meta Llama 3 Community License](META-LLAMA-3-LICENSE). Weights are downloaded on-demand only upon explicit user consent.
- **Special Thanks to the Open Source Community:**
  - [SkinTokens](https://github.com/VAST-AI-Research/SkinTokens) — AI skeleton and skin weight estimation.
  - [MoCapAnything](https://github.com/mocapanything) — Monocular video-to-motion MoCap.
  - [mesh2motion-app](https://github.com/Mesh2Motion/mesh2motion-app) — Reference animation datasets.
  - [meshoptimizer](https://github.com/zeux/meshoptimizer) & `gltfpack` — Geometry compression and LOD generation.
  - [Excalidraw](https://github.com/excalidraw/excalidraw) — Integrated brainstorming boards.
  - [ComfyUI](https://github.com/comfyanonymous/ComfyUI) — Generative node graph backend.

---

<div align="center">
  <b>Built for 3D Artists, Indie Game Developers, and AI Researchers.</b><br>
  Contributions, issues, and feature suggestions are welcome!
</div>
