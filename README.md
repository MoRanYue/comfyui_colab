# ComfyUI on Google Colab

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/MoRanYue/comfyui_colab/blob/main/ComfyUIonColab.ipynb)

Run [ComfyUI](https://github.com/comfyanonymous/ComfyUI) — a powerful and modular stable diffusion GUI — directly in **Google Colab** with GPU acceleration. This notebook provides a one-click setup, persistent storage via Google Drive, automatic dependency management, custom node installation, and public URL exposure via tunneling.

## Features

- 🚀 **One-click ComfyUI setup** — clones/updates the latest ComfyUI, installs all dependencies
- 💾 **Google Drive persistence** — models, custom nodes, and settings are saved across sessions
- 📦 **ComfyUI-Manager** — pre-installed for easy node/extension management
- 🧩 **Custom nodes** — pre-configured installations (ComfyUI-Simple-Prompt-Batcher, ComfyUI-GGUF, etc.)
- 🌐 **Public URL exposure** — two tunnel options: **Cloudflare Tunnel** and **localtunnel**
- 📥 **Model download helpers** — built-in functions for Hugging Face and CivitAI model downloads
- 🛠️ **aria2 integration** — high-speed multi-threaded downloads

## Prerequisites

- A **Google account** (for Colab and Drive)
- Sufficient **Google Drive storage** for models (recommended: at least 20 GB free)
- (Optional) A **CivitAI API token** for downloading gated CivitAI models

## Quick Start

1. **Open the notebook in Colab**  
   Click the "Open In Colab" badge above, or manually upload `ComfyUIonColab.ipynb` to [Google Colab](https://colab.research.google.com).

2. **Set runtime to GPU**  
   Go to `Runtime` → `Change runtime type` → Set `Hardware accelerator` to `T4 GPU` (or any other available GPU).

3. **Run the cells in order** — the notebook is organized into logical sections:

---

## Notebook Sections

### 1. Mount / Unmount Google Drive

- **Cell:** `mountUmount(Gdrive)`
- Mounts Google Drive to `/content/drive` for persistent storage of ComfyUI, models, and custom nodes.
- You can choose between `MOUNT` and `UNMOUNT` mode.
- **Recommended:** Keep it on `MOUNT` so all data persists after runtime disconnects.

### 2. Setup and Update ComfyUI

- Clones the official [ComfyUI repository](https://github.com/comfyanonymous/ComfyUI) into your Google Drive at `MyDrive/Colab/ComfyUI/`.
- If ComfyUI already exists, it pulls the latest updates (`git pull`).
- Installs all Python dependencies from `requirements.txt`.
- Installs [ComfyUI-Manager](https://github.com/ltdrdata/ComfyUI-Manager) for easy node management.

**Configuration options:**
| Parameter | Description | Default |
|-----------|-------------|---------|
| `DRIVE_PATH` | Path on Google Drive for Colab workspace | `/content/drive/MyDrive/Colab/` |
| `UPDATE_COMFY_UI` | Whether to update ComfyUI if it already exists | `True` |

### 3. Models Download

- Installs **aria2** (a lightweight multi-protocol download utility) for high-speed downloads.
- Loads your **CivitAI API token** from Colab Secrets (set via the 🔑 key icon in Colab).
- Provides helper functions:
  - `install_custom_node(url)` — clones a custom node repo into `custom_nodes/`
  - `download_model(url, filename)` — downloads models with aria2; automatically detects Hugging Face vs. CivitAI URLs
- Pre-configured custom nodes included:
  - [`ComfyUI-Simple-Prompt-Batcher`](https://github.com/ai-joe-git/ComfyUI-Simple-Prompt-Batcher)
  - [`ComfyUI-GGUF`](https://github.com/city96/ComfyUI-GGUF)
- **How to add your own models:** Uncomment the `download_model(...)` lines and replace the URLs with your desired models.

#### Setting up CivitAI API Token

1. In Colab, click the **key** icon in the left sidebar to open the Secrets panel.
2. Add a new secret with the name `CIVITAI_API_TOKEN` and your API token as the value.
3. Make sure to enable the secret (toggle the switch) for this notebook.

### 4. Start ComfyUI & Expose Server (Manual)

This section provides a **manual, two-step** process:

1. **Download Prerequisites** — Downloads and installs `cloudflared` (Cloudflare Tunnel client).
2. **Start ComfyUI** — Runs `python main.py --enable-manager --preview-method auto`.
   - The ComfyUI web interface will be accessible at `http://127.0.0.1:8188` within the Colab runtime.
   - Use this method if you want to connect via a **local proxy** (e.g., Colab's built-in port forwarding).

### 5. Start ComfyUI & Expose Server (Automatic)

This all-in-one section automates the entire process:

1. Downloads and installs `cloudflared`.
2. Starts ComfyUI in the background.
3. Automatically creates a **Cloudflare Tunnel** to expose the UI publicly.
4. Prints a **`https://*.trycloudflare.com`** URL that you can open in any browser.

> **Note:** Cloudflare Tunnel URLs are temporary and will change each session. Anyone with the URL can access your ComfyUI instance, so be mindful of sharing it.

### 6. localtunnel (Alternative)

An alternative tunneling method using [localtunnel](https://github.com/localtunnel/localtunnel):

1. Installs localtunnel via npm.
2. Starts ComfyUI and exposes it via a public `https://*.loca.lt` URL.
3. Also prints the server's public IP address for access control.

> **Note:** localtunnel may be slower than Cloudflare Tunnel. Use it if Cloudflare Tunnel is blocked in your region.

## Model Directory Structure

Models are stored in your Google Drive under the following paths:

```
MyDrive/Colab/ComfyUI/models/
├── checkpoints/
├── diffusion_models/
├── vaes/
├── text_encoders/
├── loras/
├── controlnet/
├── upscale_models/
├── clip/
└── ...
```

Custom nodes are stored at:
```
MyDrive/Colab/ComfyUI/custom_nodes/
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| **"No module named 'gguf'"** | The `gguf` module is missing. You can install it manually by adding `!pip install gguf` in the Models Download section. |
| **Cloudflare Tunnel hangs** | Cloudflare Tunnel may be blocked in some regions. Try the **localtunnel** method instead (Section 6). |
| **Out of GPU memory** | Reduce image resolution or batch size in your ComfyUI workflow. Use `--lowvram` flag if needed. |
| **Drive not mounted** | Re-run the Mount cell and ensure you authorize Google Drive access. |
| **Runtime disconnects** | Colab free tier has time limits. Consider Colab Pro/Pro+ for longer sessions. |
| **ComfyUI fails to start** | Check the cell output for error messages. Common causes: missing dependencies or disk space. |

## Tips

- 💡 **Persist custom models:** Place your `.safetensors` / `.ckpt` files in the appropriate `models/` subfolder on Google Drive before running the notebook.
- 🔄 **Update ComfyUI:** Set `UPDATE_COMFY_UI = True` or manually run `git pull` in the ComfyUI directory.
- 🧹 **Free up space:** Delete unused models from Google Drive periodically to avoid running out of storage.
- 🔐 **Secure your tunnel:** Both Cloudflare and localtunnel create **unauthenticated public URLs**. Consider using ComfyUI's built-in authentication or stopping the tunnel when not in use.

## License

This notebook is provided for educational and research purposes. ComfyUI is licensed under the GPL-3.0 License. Please respect the licenses of any models and custom nodes you download.
