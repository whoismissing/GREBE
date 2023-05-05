FROM ubuntu:20.04

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y && apt-get upgrade -y
# Install system dependencies for kernel and gcc building
RUN apt-get install -y make git \
    wget \
    xz-utils \
    patch \
    flex bison build-essential bc libssl-dev libelf-dev \
    gcc libncurses-dev sudo 
# Install system dependencies for 32-bit library support
RUN dpkg --add-architecture i386 && apt-get update -y
RUN apt-get install lib32z1 gcc-multilib -y

ARG UNAME=user
ARG UID=1000
ARG GID=1000
RUN set -x && groupadd -g ${GID} -o ${UNAME} && \
    useradd -u ${UID} -g ${GID} -G sudo -ms /bin/bash ${UNAME} && \
    echo "${UNAME} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers

USER ${UNAME}
WORKDIR /home/${UNAME}
# Clone and build GREBE's custom gcc 
RUN git clone https://github.com/Markakd/GREBE.git
RUN cd /home/${UNAME}/GREBE/gcc-9.3.0 && ./contrib/download_prerequisites
RUN cd /home/${UNAME}/GREBE/gcc-9.3.0 && mkdir -p gcc-bin && mkdir -p gcc-build
RUN cd /home/${UNAME}/GREBE/gcc-9.3.0/gcc-build && ../configure --prefix=/home/${UNAME}/GREBE/gcc-9.3.0/gcc-bin --enable-languages=c,c++ --enable-multilib && make -j `nproc`
RUN cd /home/${UNAME}/GREBE/gcc-9.3.0/gcc-build && sudo make install
# Swap out the system's default compiler with GREBE's custom GCC
RUN sudo update-alternatives --install /usr/bin/gcc gcc /home/${UNAME}/GREBE/gcc-9.3.0/gcc-bin/bin/gcc 50

CMD ["bash"]
