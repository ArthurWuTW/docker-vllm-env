FROM nvidia/cuda:12.6.3-cudnn-devel-rockylinux9

ARG HF_TOKEN

RUN yum install -y ncurses git
RUN yum install -y python3.12 python3.12-pip python3.12-devel
RUN yum install -y libnccl libnccl-devel libnccl-static

RUN pip3.12 install uv
RUN uv pip install --python 3.12 vllm --torch-backend=auto --system
ENV LD_LIBRARY_PATH=/usr/local/lib/python3.12/site-packages/cusparselt/lib:${LD_LIBRARY_PATH}


RUN dnf install -y openssh-server passwd && dnf clean all

RUN echo "root:root123" | chpasswd
RUN mkdir /var/run/sshd && ssh-keygen -A
RUN sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config \
    && sed -i 's/^#PermitUserEnvironment no/PermitUserEnvironment yes/' /etc/ssh/sshd_config

RUN echo 'LD_LIBRARY_PATH=/usr/local/lib/python3.12/site-packages/cusparselt/lib:${LD_LIBRARY_PATH}' >> /etc/environment
RUN echo 'NVIDIA_VISIBLE_DEVICES=all' >> /etc/environment
RUN echo "HF_TOKEN=${HF_TOKEN}" >> /etc/environment

CMD ["/usr/sbin/sshd", "-D"]
