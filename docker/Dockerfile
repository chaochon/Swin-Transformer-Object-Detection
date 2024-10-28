FROM nvcr.io/nvidia/pytorch:24.08-py3

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

# Install openmim and mmengine
RUN pip install mmengine

# Install opencv-python
RUN pip uninstall -y $(pip list --format=freeze | grep opencv) &&  rm -rf /usr/local/lib/python3.10/dist-packages/cv2/
RUN pip install opencv-python-headless

# Install mmcv from source
RUN git clone -b v2.1.0 https://github.com/open-mmlab/mmcv.git /mmcv
WORKDIR /mmcv
RUN pip install -r requirements/optional.txt
RUN pip install -e . -v

# Install MMDetection from source
RUN git clone https://github.com/open-mmlab/mmdetection.git /mmdetection
WORKDIR /mmdetection
ENV FORCE_CUDA="1"
RUN pip install -r requirements/build.txt
RUN pip install --no-cache-dir -e . -v

CMD ["/usr/bin/bash"]