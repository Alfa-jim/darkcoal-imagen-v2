# Slim base: runpod/base already has CUDA 12.1 runtime but NOT a bloated torch.
# That keeps the image ~2–3 GB instead of 12 GB (runpod/pytorch:* is huge and caused 40min pull+Unhealthy).
FROM runpod/base:0.6.2-cuda12.1.0

ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    # Persist HF downloads on RunPod Network Volume (survives cold starts; without volume it's just a local dir)
    HF_HOME=/runpod-volume \
    HF_HUB_CACHE=/runpod-volume

WORKDIR /app

# System deps: libgl for opencv (Pillow's JPEG), git for diffusers hf downloads
RUN apt-get update && apt-get install -y --no-install-recommends git libgl1 libglib2.0-0 && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
# --extra-index-url for CUDA wheels is inside requirements.txt (first line), so plain pip install works.
RUN pip install --upgrade pip && pip install --no-cache-dir -r requirements.txt

COPY handler.py .

# No ENTRYPOINT trick — RunPod expects CMD python handler.py and greps for runpod.serverless.start
CMD ["python", "-u", "handler.py"]
