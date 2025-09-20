FROM nvidia/cuda:12.6.3-cudnn-devel-rockylinux9

ARG HF_TOKEN
ARG VSCODE_COMMIT_ID

#vscode server
RUN yum install -y procps-ng tar gzip curl --allowerasing
RUN yum install -y gcc gcc-c++ make cmake
RUN yum install -y ncurses git
RUN yum install -y python3.12 python3.12-pip python3.12-devel
RUN yum install -y libnccl libnccl-devel libnccl-static
RUN yum install -y epel-release
RUN yum install -y ccache

RUN pip3.12 install uv
RUN uv pip install --python 3.12 vllm --torch-backend=auto --system
ENV LD_LIBRARY_PATH=/usr/local/lib/python3.12/site-packages/nvidia/cusparselt/lib:usr/lib64:/usr/local/lib/python3.12/site-packages/cusparselt/lib:${LD_LIBRARY_PATH}


RUN dnf install -y openssh-server passwd && dnf clean all

RUN echo "root:root123" | chpasswd
RUN mkdir /var/run/sshd && ssh-keygen -A
RUN sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#PermitUserEnvironment no/PermitUserEnvironment yes/' /etc/ssh/sshd_config

RUN echo 'LD_LIBRARY_PATH=/usr/local/lib/python3.12/site-packages/nvidia/cusparselt/lib:/usr/lib64:/usr/local/lib/python3.12/site-packages/cusparselt/lib:${LD_LIBRARY_PATH}' >> /etc/environment
RUN echo 'NVIDIA_VISIBLE_DEVICES=all' >> /etc/environment
RUN echo "HF_TOKEN=${HF_TOKEN}" >> /etc/environment

# vscode server
RUN mkdir -p /root/.vscode-server/bin/${VSCODE_COMMIT_ID}
RUN curl -L https://update.code.visualstudio.com/commit:${VSCODE_COMMIT_ID}/server-linux-x64/stable \
    | tar --strip-components=1 -xz -C /root/.vscode-server/bin/${VSCODE_COMMIT_ID}

# vllm project
RUN git clone https://github.com/vllm-project/vllm
RUN pip3.12 install setuptools_scm

# Build python only
RUN cd /vllm && VLLM_USE_PRECOMPILED=1 uv pip install --python 3.12 --editable . --system -v

CMD ["/usr/sbin/sshd", "-D"]
