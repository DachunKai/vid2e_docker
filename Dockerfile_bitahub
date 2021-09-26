FROM pytorch/pytorch:1.3-cuda10.1-cudnn7-devel
# FROM ubuntu:18.04
# https://hub.docker.com/r/nvidia/cuda
# bithuab's debug mode uses gtx1080ti
# cuda version which gtx1080ti supports <= 10.1
# torch version which cuda 10.1 supports <= 1.6.0

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

ARG user=liam
ARG home=/home/$user
ARG github=https://hub.fastgit.org

RUN sed -i s/archive.ubuntu.com/mirrors.ustc.edu.cn/g /etc/apt/sources.list \
      && apt-get -y update \
      && apt-get -y install \
      openssh-server \
      sudo \
      vim \ 
      zsh tmux \
      git \
      && sed -i 's/^#PermitRootLogin .*/PermitRootLogin yes/' \
      /etc/ssh/sshd_config \
      && ssh-keygen -A \
      && useradd -ms/bin/bash -k/dev/null -d$home $user \
      && echo $user:$user | chpasswd \
      && echo root:root | chpasswd \
      && gpasswd -a$user sudo \
      && echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
      && rm -rf $home/* \
      && git clone https://gitee.com/dachunkai/rpg_vid2e.git $home/rpg_vid2e/ \
      && conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/  \
      && conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/  \
      && conda config --set show_channel_urls yes \
      && conda config --add channels https://mirrors.ustc.edu.cn/anaconda/cloud/conda-forge/ \
      && sed -i '5d' ~/.condarc \
      && conda config --set channel_priority flexible \
      && conda update conda \
      && pip config set global.index-url https://mirrors.ustc.edu.cn/pypi/web/simple \
      && conda create -y --name vid2e --file $home/rpg_vid2e/requirements.txt \
      && rm -rf /var/lib/apt/lists/* /var/cache/* /tmp/* /var/tmp/*

WORKDIR $home
# bitahub will create some directories which need root privilege
USER root
