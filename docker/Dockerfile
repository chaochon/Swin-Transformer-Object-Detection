#PYTORCH="1.5.0" CUDA="10.2" CUDNN="7.6"
FROM nvcr.io/nvidia/pytorch:20.03-py3
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Shanghai

ENV TORCH_CUDA_ARCH_LIST="7.0 8.0 8.6+PTX"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

RUN apt-get update && apt-get install -y ffmpeg libsm6 libxext6 git ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6  \
    openssh-server openssl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install openSSH && openSSL
RUN mkdir /var/run/sshd
RUN cat /etc/ssh/ssh_config | grep -v StrictHostKeyChecking > /etc/ssh/ssh_config.new && \
    echo "    StrictHostKeyChecking no" >> /etc/ssh/ssh_config.new && \
    cat /etc/ssh/sshd_config | grep -v PermitRootLogin > /etc/ssh/sshd_config.new && \
    echo "PermitRootLogin yes" >> /etc/ssh/sshd_config.new && \
    mv -f /etc/ssh/ssh_config.new /etc/ssh/ssh_config && \
    mv -f /etc/ssh/sshd_config.new /etc/ssh/sshd_config > /dev/null 2>&1;

# Install mmcv from source
RUN pip install mmcv-full==1.2.7 -i https://pypi.tuna.tsinghua.edu.cn/simple
#RUN git clone -b v1.2.7 https://github.com/open-mmlab/mmcv.git /mmcv
#WORKDIR /mmcv
#RUN pip install -r requirements/optional.txt
#RUN pip install -e . -v

# Install MMDetection from source
RUN git clone -b v2.10.0 https://github.com/open-mmlab/mmdetection.git /mmdetection
WORKDIR /mmdetection
ENV FORCE_CUDA="1"
RUN pip install -r requirements/build.txt
RUN pip install --no-cache-dir -e . -v

# Install numpy==1.22.4
RUN pip uninstall numpy && pip install numpy==1.22.4 -i https://pypi.tuna.tsinghua.edu.cn/simple

# setup for this program
WORKDIR /home/caochong/experiments/Swin-Transformer-Object-Detection
RUN pip install -e . -v

CMD ["/usr/bin/bash"]