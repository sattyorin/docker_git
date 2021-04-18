FROM ubuntu:18.04

SHELL ["/bin/bash", "-c"]
ENV DEBIAN_FRONTEND noninteractive

# 各環境変数を設定
ENV USER buffet
ENV HOME /home/${USER}
ENV SHELL /bin/bash
ENV ROS_DISTRO dashing

RUN apt-get update && \
    apt-get install -y apt-utils && \
    apt-get upgrade -y

# setup timezone
RUN echo 'Asia/Tokyo' > /etc/timezone && \
    ln -s /usr/share/zoneinfo/Etc/GMT+9 /etc/localtime && \
    apt-get update && apt-get install -q -y tzdata && rm -rf /var/lib/apt/lists/*

# setup langage
RUN apt-get update && \
    apt-get install -y locales
RUN locale-gen en_US en_US.utf8
RUN update-locale LC_ALL=en_US.utf8 LANG=en_US.utf8
ENV LANG en_US.utf8

# 一般ユーザーアカウントを追加
RUN useradd -s ${SHELL} -m ${USER}
RUN gpasswd -a ${USER} sudo
RUN echo "${USER}:hoge" | chpasswd

# completion
RUN apt-get update && apt-get install -y curl
WORKDIR /etc/bash_completion.d/
RUN curl -L https://raw.githubusercontent.com/docker/compose/1.25.5/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose && \
    curl -L https://raw.githubusercontent.com/docker/machine/v0.16.0/contrib/completion/bash/docker-machine.bash -o /etc/bash_completion.d/docker-machine
WORKDIR /

# APTソースリストの設定
RUN apt-get update && \
    apt-get install -y gnupg2 lsb-release && \
    curl http://repo.ros2.org/repos.key | apt-key add - && \
    sh -c 'echo "deb [arch=amd64,arm64] http://packages.ros.org/ros2/ubuntu \
    `lsb_release -cs` main" > /etc/apt/sources.list.d/ros2-latest.list' && \
    apt-get update

# ROS2パッケージのインストール
RUN apt-get install -y ros-${ROS_DISTRO}-ros-base \
    python3-argcomplete \
    python3-colcon-common-extensions \
    python3-rosdep \
    python3-argcomplete
RUN rosdep init

USER ${USER}
RUN rosdep update

RUN source /opt/ros/${ROS_DISTRO}/setup.bash
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /home/${USER}/.bashrc
RUN mkdir -p /home/${USER}/dev_ws/src 
USER root
WORKDIR /home/${USER}/dev_ws/
RUN rosdep install -i --from-path src --rosdistro ${ROS_DISTRO} -y
USER ${USER}
WORKDIR /home/${USER}/dev_ws
RUN colcon build

# eProsima Fast RTPS
USER root
RUN apt-get update && apt-get install -y wget \
    libasio-dev \
    libtinyxml2-dev \
    python3-pip \
    python3-vcstool \
    libasio-dev \
    git
RUN pip3 install -U colcon-common-extensions vcstool
USER ${USER}
WORKDIR /home/${USER}
RUN mkdir -p /home/${USER}/Fast_RTPS/src
WORKDIR /home/${USER}/Fast_RTPS
RUN wget https://raw.githubusercontent.com/eProsima/Fast-RTPS/master/fastrtps.repos && \
    vcs import src < fastrtps.repos && \
    colcon build
# USER ${USER}
# WORKDIR /home/${USER}
# RUN git clone https://github.com/eProsima/Fast-CDR.git
# RUN mkdir -p /home/${USER}/Fast-CDR/build
# WORKDIR /home/${USER}/Fast-CDR/build
# RUN cmake ..
# USER root
# RUN mkdir -p /usr/local/share/fastcdr
# USER ${USER}
# WORKDIR /home/${USER}/Fast-CDR/build
# # RUN cmake --build . --target install
# RUN make
# USER root
# WORKDIR /home/${USER}/Fast-CDR/build
# RUN make install
# USER ${USER}
# WORKDIR /home/${USER}
# RUN git clone https://github.com/eProsima/foonathan_memory_vendor.git
# RUN mkdir -p /home/${USER}/foonathan_memory_vendor/build/
# WORKDIR /home/${USER}/foonathan_memory_vendor/build/
# RUN cmake ..
# # RUN cmake --build . --target install
# RUN make
# USER root
# WORKDIR /home/${USER}/foonathan_memory_vendor/build
# RUN make install
# USER ${USER}
# WORKDIR /home/${USER}
# RUN git clone https://github.com/eProsima/Fast-RTPS.git
# RUN mkdir -p /home/${USER}/Fast-RTPS/build
# WORKDIR /home/${USER}/Fast-RTPS/build
# RUN cmake .. 
# # RUN cmake --build . --target install
# RUN make
# USER root
# WORKDIR /home/${USER}/Fast-RTPS/build
# RUN make install

USER root
# Micro-XRCE-DDS-Agent
RUN apt-get update && apt-get install -y build-essential

USER ${USER}
WORKDIR /home/${USER}/
RUN  git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git
WORKDIR /home/${USER}/Micro-XRCE-DDS-Agent
RUN mkdir -p /home/${USER}/Micro-XRCE-DDS-Agent/build
WORKDIR /home/${USER}/Micro-XRCE-DDS-Agent/build
RUN cmake -DTHIRDPARTY=ON ..
RUN make
USER root
WORKDIR /home/${USER}/Micro-XRCE-DDS-Agent/build
RUN make install && \
    ldconfig /usr/local/lib/


USER root
WORKDIR /