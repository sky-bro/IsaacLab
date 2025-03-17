ARG LAB_VERSION_TAG="latest"

FROM isaac-lab-base:${LAB_VERSION_TAG}

# Set NVIDIA paths
ARG CONDA_ROOT="/root/miniconda3"
ENV PATH="${CONDA_ROOT}/bin:/usr/local/nvidia/bin:$PATH"
ENV LD_LIBRARY_PATH="/usr/local/nvidia/lib64"

# Link NVIDIA binaries
RUN ln -sf /usr/local/nvidia/bin/nvidia* /usr/bin

# Install Ray and configure it
RUN /workspace/isaaclab/_isaac_sim/python.sh -m pip install "ray[default, tune]"==2.43.0 && \
sed -i "1i $(echo "#!/workspace/isaaclab/_isaac_sim/python.sh")" \
/isaac-sim/kit/python/bin/ray && ln -s /isaac-sim/kit/python/bin/ray /usr/local/bin/ray

# Install tuning dependencies
RUN /workspace/isaaclab/_isaac_sim/python.sh -m pip install optuna bayesian-optimization

# Install MLflow for logging
RUN /workspace/isaaclab/_isaac_sim/python.sh -m pip install mlflow

RUN apt update && apt install -y wget && \
    apt -y autoremove && apt clean autoclean && \
    rm -rf /var/lib/apt/lists/*

# Install Miniconda on x86 or ARM platforms
RUN arch=$(uname -m) && \
    if [ "$arch" = "x86_64" ]; then \
    MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh"; \
    elif [ "$arch" = "aarch64" ]; then \
    MINICONDA_URL="https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh"; \
    else \
    echo "Unsupported architecture: $arch"; \
    exit 1; \
    fi && \
    wget $MINICONDA_URL -O miniconda.sh && \
    mkdir -p /root/.conda && \
    bash miniconda.sh -b -p ${CONDA_ROOT} && \
    rm -f miniconda.sh

RUN conda init && conda config --set auto_activate_base false

# export variables
RUN echo "export OMNI_KIT_ALLOW_ROOT=1" >> /root/.bashrc
RUN echo "export HF_ENDPOINT=https://hf-mirror.com" >> /root/.bashrc
