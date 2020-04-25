FROM ubuntu:18.04

SHELL ["/bin/bash", "-c"]
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y apt-utils

# setup timezone
RUN echo 'Asia/Tokyo' > /etc/timezone && \
    ln -s /usr/share/zoneinfo/Etc/GMT+9 /etc/localtime && \
    apt-get update && apt-get install -q -y tzdata && rm -rf /var/lib/apt/lists/*

# 各環境変数を設定
ENV USER buffet
ENV HOME /home/${USER}
ENV SHELL /bin/bash
ENV ROS_DISTRO eloquent

# 一般ユーザーアカウントを追加
RUN useradd -s ${SHELL} -m ${USER}
RUN gpasswd -a ${USER} sudo
RUN echo "${USER}:p1zzateam@CR" | chpasswd

RUN apt-get update
RUN apt-get install -y sudo

# ROS2 set up
RUN apt-get install -y language-pack-ja-base language-pack-ja
RUN locale-gen ja_JP ja_JP.UTF-8
# RUN update-locale LC_ALL=jp_JP.UTF-8 LANG=jp_JP.UTF-8
ENV LANG ja_JP.utf8
ENV LC_ALL ja_JP.utf8
# RUN apt-get update && apt-get install locales && locale -	a
# ENV LANGUAGE ja_JP:ja
# RUN apt-get install -y sed && sed -e "s/    SendEnv LANG LC_*/#   SendEnv LANG LC_*/" /etc/ssh/ssh_config
RUN apt-get update &&  apt-get install -y curl gnupg2 lsb-release && curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -
RUN sh -c 'echo "deb http://packages.ros.org/ros2/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/ros2-latest.list'
RUN apt-get update && apt-get install -y \
	build-essential \
	cmake \
	git
RUN apt-get update && apt-get install -y \
	python3-colcon-common-extensions \
	python3-pip \
	python-rosdep \
	python3-vcstool \
	wget

RUN rosdep init
# RUN apt-get install -y ros-${ROS_DISTRO}-desktop

# RUN su - ${USER}
USER ${USER}

RUN mkdir -p /home/${USER}/ros2_${ROS_DISTRO}/src
WORKDIR /home/${USER}/ros2_${ROS_DISTRO}
RUN wget https://raw.githubusercontent.com/ros2/ros2/${ROS_DISTRO}/ros2.repos
RUN vcs import src < ros2.repos
RUN rosdep update
# https://answers.ros.org/question/241112/how-to-run-rosdep-init-and-update-in-dockerfile/
USER root
RUN apt-get update
RUN rosdep install --from-paths src --ignore-src --rosdistro eloquent -y --skip-keys "console_bridge fastcdr fastrtps libopensplice67 libopensplice69 rti-connext-dds-5.3.1 urdfdom_headers"
RUN apt-get install -y libasio-dev
RUN apt-get update
USER ${USER}
WORKDIR /home/${USER}/ros2_${ROS_DISTRO}/
RUN colcon build --symlink-install

RUN source /opt/ros/${ROS_DISTRO}/setup.bash
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> ~/home/${USER}/.bashrc
RUN mkdir dev_ws && mkdir dev_ws/src 
WORKDIR /home/${USER}/dev_ws
RUN colcon build