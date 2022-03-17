FROM nvidia/cuda:11.3.1-cudnn8-devel-ubuntu20.04

ENV PATH /opt/conda/bin:$PATH
ENV LD_LIBRARY_PATH /usr/local/cuda/extras/CUPTI/lib64:/usr/local/cuda/compat/lib:/usr/local/nvidia/lib:/usr/local/nvidia/lib64:/usr/lib/x86_64-linux-gnu:/usr/local/cuda-11.3/lib64:$LD_LIBRARY_PATH

COPY ./miniconda.sh /root 

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

RUN sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list && \
    APT_INSTALL="apt-get install -y --no-install-recommends" && \
    PIP_INSTALL="python -m pip --no-cache-dir install --upgrade" && \
    GIT_CLONE="git clone --depth 10" && \
    rm -rf /var/lib/apt/lists/* \
           /etc/apt/sources.list.d/cuda.list \
           /etc/apt/sources.list.d/nvidia-ml.list && \
    apt-get update && \
    apt-get upgrade -y && \
# ==================================================================
# tools
# ------------------------------------------------------------------
    DEBIAN_FRONTEND=noninteractive $APT_INSTALL \
        apt-utils \
        build-essential \
        ca-certificates \
        wget \
        git \
        tmux \
	zsh \
        cmake \
	openssh-server \
        g++ \
        gcc \
        tree \
        vim-gtk \
        libboost-dev \
        libboost-thread-dev \
        libboost-filesystem-dev \
        libglib2.0-0 \
        libsm6 \
        libxext6 \
        libxrender-dev \
        libgl1-mesa-glx \
        && \
# ================================================================
# ssh config
# ================================================================
    sed -i 's/^#PermitRootLogin .*/PermitRootLogin yes/' /etc/ssh/sshd_config && \
    sed -i 's/^#PermitEmptyPasswords .*/PermitEmptyPasswords yes/' /etc/ssh/sshd_config && \
    ssh-keygen -A && \
    echo root:root | chpasswd && \
# =================================================================
# Miniconda3
# ----------------------------------------------------------------
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
# ==================================================================
# pip & config
# ------------------------------------------------------------------
    pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple && \
    rm -rf ~/.condarc && \
    conda config --add channels pytorch && \
    conda config --set channel_priority flexible && \
    conda config --set ssl_verify false && \
    conda update conda && \
# ================================================================
# dependencys for my environment
    conda create -y --name vid2e python=3.7 && \
    /bin/bash -c "source activate vid2e && conda install pytorch torchvision torchaudio cudatoolkit=11.3 -c pytorch && conda install -y -c conda-forge pybind11 matplotlib && pip --no-cache-dir install opencv-python-headless pyyaml argparse h5py numpy pandas scikit-image tensorboardX tqdm Pillow pytorch-lightning cupy-cuda113" && \
    echo "source activate vid2e" > ~/.bashrc && \
# =================================================================
# cleanup
#-----------------------------------------------------------------
    ldconfig && \
    apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/*
