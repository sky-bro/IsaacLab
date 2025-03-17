ARG LAB_VERSION_TAG="latest"

FROM isaac-lab-base:${LAB_VERSION_TAG}

# WGet is needed so that GCS or other cloud providers can mark the container as ready.
# Otherwise the Ray liveliness checks fail.
RUN apt-get update && apt-get install wget

# Set NVIDIA paths
ENV PATH="/usr/local/nvidia/bin:$PATH"
ENV LD_LIBRARY_PATH="/usr/local/nvidia/lib64"

# Link NVIDIA binaries
RUN ln -sf /usr/local/nvidia/bin/nvidia* /usr/bin

# Install Ray and configure it
# fix ray runtime env
RUN cp /workspace/isaaclab/_isaac_sim/setup_python_env.sh /workspace/isaaclab/_isaac_sim/setup_python_env.sh.bak && \
    sed -i '/^export PYTHONPATH=/d' /workspace/isaaclab/_isaac_sim/setup_python_env.sh && \
    echo 'export PYTHONPATH=$PYTHONPATH:$SCRIPT_DIR/../../../$PYTHONPATH:$SCRIPT_DIR/kit/python/lib/python3.10:$SCRIPT_DIR/kit/python/lib/python3.10/site-packages:$SCRIPT_DIR/python_packages:$SCRIPT_DIR/exts/isaacsim.simulation_app:$SCRIPT_DIR/extsDeprecated/omni.isaac.kit:$SCRIPT_DIR/kit/kernel/py:$SCRIPT_DIR/kit/plugins/bindings-python' >> /workspace/isaaclab/_isaac_sim/setup_python_env.sh 

RUN /workspace/isaaclab/_isaac_sim/python.sh -m pip install "ray[default, tune]"==2.43.0 && \
sed -i "1i $(echo "#!/workspace/isaaclab/_isaac_sim/python.sh")" \
/isaac-sim/kit/python/bin/ray && ln -s /isaac-sim/kit/python/bin/ray /usr/local/bin/ray


RUN mv /workspace/isaaclab/_isaac_sim/setup_python_env.sh.bak /workspace/isaaclab/_isaac_sim/setup_python_env.sh

# Install tuning dependencies
RUN /workspace/isaaclab/_isaac_sim/python.sh -m pip install optuna bayesian-optimization

# Install MLflow for logging
RUN /workspace/isaaclab/_isaac_sim/python.sh -m pip install mlflow

# Initialize once, sync registry
RUN /workspace/isaaclab/isaaclab.sh -p /workspace/isaaclab/scripts/reinforcement_learning/rl_games/train.py --task=Isaac-Cartpole-v0 || pwd
