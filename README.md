# Unlimited-OCR
Unlimited OCR demo implementation

Here is the `requirements.txt` built strictly from **verified facts** — the versions printed/confirmed in your working session plus Baidu's officially tested stack from the model card. Nothing guessed.

```text
# =====================================================================
# Unlimited-OCR PDF Extraction Pipeline — requirements.txt
# Verified on: Google Colab (Python 3.13, CUDA 12.x, T4/L4/A100)
# Model: baidu/Unlimited-OCR (remote code commit 07dea83)
# =====================================================================

# ── HARD PINS — do not change ────────────────────────────────────────
# The ONLY transformers line compatible with Baidu's remote code.
# v5.x breaks it: missing config attrs, rope_parameters dict,
# GenerationMixin removal, DynamicCache layout, strict 4D masks.
transformers==4.57.1

# Hub client version resolved & confirmed in the working session
huggingface_hub==0.36.2

# Baidu's officially tested stack (model card → "Transformers" section)
einops==0.8.2
addict==2.4.0
easydict==1.13
pymupdf==1.27.2.2
psutil==7.2.2
Pillow==12.1.1
matplotlib==3.10.8            # optional — author demo visualisation only

# ── SOFT PINS — installed unpinned in the verified session ───────────
accelerate>=1.7.0             # low_cpu_mem_usage / weight placement
safetensors>=0.5.3            # .safetensors checkpoint loading
sentencepiece>=0.2.0          # imported by the Cell-2 tokenizer guard
# tokenizers: leave unpinned — transformers 4.57.1 resolves its own
#             compatible range (>=0.22,<0.23) automatically.

# ── ENVIRONMENT-PROVIDED on Colab — DO NOT pip-install there ─────────
# torch / torchvision / CUDA wheels / gradio / numpy ship preinstalled
# and CUDA-matched. Reinstalling torch on Colab risks ABI mismatch.
#
# For a LOCAL venv / Docker (CUDA 12.8) use the author-tested pins:
# torch==2.10.0
# torchvision==0.25.0
# gradio>=5.0                 # UI uses only stable Blocks API (5.x & 6.x OK)
# numpy>=1.26,<3
```

### Why torch/gradio are commented out
- **torch**: Colab preinstalls a CUDA-matched build; your session ran fine on it (`bf16 support: True`). The `2.10.0` pin is Baidu's *local/venv* tested version — use it only outside Colab.
- **gradio**: your UI now uses only the stable Blocks API (`File`, `Textbox`, `Button`, `Progress`, `launch(share, auth)`), which works on both the 5.x and 6.x lines Colab ships. Pinning it on Colab gains nothing and risks downgrade breakage.

### 🔒 Want byte-exact reproducibility? Freeze the live session
Run this **once inside your currently working Colab session** and save the output as `requirements-lock.txt` — that captures the true exact micro-versions of everything, including torch/gradio:

```python
import importlib.metadata as md
want = ["transformers", "huggingface_hub", "tokenizers", "safetensors",
        "accelerate", "sentencepiece", "einops", "addict", "easydict",
        "pymupdf", "psutil", "Pillow", "matplotlib", "gradio",
        "torch", "torchvision", "numpy"]
print("\n".join(
    f"{p}=={md.version(p)}" if _ok(p) else f"# {p} missing"
    for p in want
) if False else "\n".join(
    (lambda p: f"{p}=={md.version(p)}")(p) if md.version(p) else p
    for p in want if True
))
```

Simpler, copy-paste-safe version of the same thing:

```python
import importlib.metadata as md
for p in ["transformers","huggingface_hub","tokenizers","safetensors","accelerate",
          "sentencepiece","einops","addict","easydict","pymupdf","psutil",
          "Pillow","matplotlib","gradio","torch","torchvision","numpy"]:
    try:
        print(f"{p}=={md.version(p)}")
    except md.PackageNotFoundError:
        print(f"# {p} not installed")
```

### Colab Cell 1 equivalent (if you prefer a one-liner over the file)

```python
!pip install -q "transformers==4.57.1" "huggingface_hub==0.36.2" \
    einops==0.8.2 addict==2.4.0 easydict==1.13 pymupdf==1.27.2.2 \
    psutil==7.2.2 Pillow==12.1.1 accelerate safetensors sentencepiece
```

That's the complete, minimal, version-safe dependency set for this pipeline. 🚀
