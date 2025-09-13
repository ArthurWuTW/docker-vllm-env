FROM nvcr.io/nvidia/pytorch:23.10-py3

ENV DEBIAN_FRONTEND=noninteractive

ARG VSCODE_COMMIT_ID
ARG HF_TOKEN

# 基本工具
RUN apt-get update -y && apt-get install -y \
    procps tar gzip curl \
    ccache \
    openssh-server


# SSH 設定
RUN echo "root:root123" | chpasswd && mkdir -p /var/run/sshd && ssh-keygen -A \
    && sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#PermitUserEnvironment.*/PermitUserEnvironment yes/' /etc/ssh/sshd_config


# 安裝 VSCode server
RUN mkdir -p /root/.vscode-server/bin/${VSCODE_COMMIT_ID} && \
    curl -L https://update.code.visualstudio.com/commit:${VSCODE_COMMIT_ID}/server-linux-x64/stable \
    | tar --strip-components=1 -xz -C /root/.vscode-server/bin/${VSCODE_COMMIT_ID}

# clone vLLM
RUN git clone https://github.com/vllm-project/vllm 

# TOKEN Config
RUN echo "export HF_TOKEN=${HF_TOKEN}" >> /etc/bash.bashrc

CMD ["/usr/sbin/sshd", "-D"]
