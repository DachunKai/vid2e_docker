FROM nvidia/cuda:11.1-cudnn8-devel-ubuntu18.04

ENV PATH /opt/conda/bin:$PATH
ENV LD_LIBRARY_PATH /usr/local/cuda/extras/CUPTI/lib64:/usr/local/cuda/compat/lib:/usr/local/nvidia/lib:/usr/local/nvidia/lib64:/usr/lib/x86_64-linux-gnu:/usr/local/cuda-11.1/lib64:$LD_LIBRARY_PATH

COPY ./miniconda.sh /root 
COPY ./requirements.txt /root
COPY ./rpg_vid2e/ /root/rpg_vid2e
COPY ./torch-1.10.2+cu111-cp37-cp37m-linux_x86_64.whl /root

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
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/ && \
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/ && \
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/ && \
    conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/ && \
    conda config --set show_channel_urls yes && \
    sed -i '6d' ~/.condarc && \
    conda config --set channel_priority flexible && \
    conda config --set ssl_verify false && \
    conda update conda && \
# ================================================================
# dependencys for my environment
    conda create -y --name vid2e --file /root/requirements.txt && \
    /bin/bash -c "source activate vid2e && conda install -y -c conda-forge pybind11 matplotlib && pip --no-cache-dir install opencv-python-headless pyyaml argparse h5py numpy pandas scikit-image tensorboardX tqdm Pillow pytorch-lightning cupy-cuda111 && pip uninstall -y torch && pip install --no-cache-dir install /root/torch-1.10.2+cu111-cp37-cp37m-linux_x86_64.whl torchvision && pip install /root/rpg_vid2e/esim_py/" && \
    echo "source activate vid2e" > ~/.bashrc && \
# =================================================================
# cleanup
#-----------------------------------------------------------------
    ldconfig && \
    apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* ~/*
