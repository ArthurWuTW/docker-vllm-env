FROM nvidia/cuda:12.3.2-cudnn9-runtime-rockylinux9


ARG HOSTUSER
ARG HOSTUID
ARG HOSTGID

RUN groupadd -g ${HOSTGID} ${HOSTUSER}
RUN useradd ${HOSTUSER} -u ${HOSTUID} -g ${HOSTGID} -m

RUN yum install -y ncurses git
RUN yum install -y python3.12 python3.12-pip
RUN yum install -y libnccl libnccl-devel libnccl-static

RUN pip3.12 install torch==2.3.1 torchvision==0.18.1 torchaudio==2.3.1 --index-url https://download.pytorch.org/whl/cu121

# Install vLLM dependencies
RUN yum install -y python3.12-devel
RUN pip3.12 install vllm

