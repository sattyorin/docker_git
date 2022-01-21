# ubuntu16.04(tensorflow/tensorflow:1.12.0)だとopenGLが上手くいかない。
FROM tensorflow/tensorflow:1.15.0-gpu-py3

RUN apt-get update

RUN apt-get install -y nano build-essential libssl-dev libffi-dev python3-dev libosmesa6-dev wget unzip libgl1-mesa-glx libglfw3 git sudo libglew-dev
# RUN apt-get install -y python3-tk
RUN apt install -y --no-install-recommends mesa-utils x11-apps && rm -rf /var/lib/apt/lists/*

ENV USER sattyorin
ENV HOME /home/${USER}
ENV SHELL /bin/bash
RUN useradd -s ${SHELL} -m ${USER}
RUN gpasswd -a ${USER} sudo
RUN echo "${USER}:hoge" | chpasswd

RUN mkdir ${HOME}/.mujoco
RUN wget https://www.roboti.us/file/mjkey.txt -P ${HOME}/.mujoco/.
RUN wget https://www.roboti.us/download/mjpro150_linux.zip -P ${HOME}/.mujoco/.
RUN unzip ${HOME}/.mujoco/mjpro150_linux.zip -d ${HOME}/.mujoco/.
RUN rm -r ${HOME}/.mujoco/mjpro150_linux.zip

RUN echo "alias python=\"python3\"" >> ${HOME}/.bashrc
RUN echo "alias pip=\"pip3\"" >> ${HOME}/.bashrc
RUN echo "export MUJOCO_PY_MJKEY_PATH=~/.mujoco/mjkey.txt" >> ${HOME}/.bashrc
RUN echo "export MUJOCO_PY_MJPRO_PATH=~/.mujoco/mjpro150" >> ${HOME}/.bashrc
RUN echo "export LD_LIBRARY_PATH=~/.mujoco/mjpro150/bin" >> ${HOME}/.bashrc
RUN echo "export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-10.0/targets/x86_64-linux/lib" >> ${HOME}/.bashrc
RUN echo "alias MJPL='LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libGLEW.so:/usr/lib/x86_64-linux-gnu/libGL.so'" >> ${HOME}/.bashrc
RUN . ${HOME}/.bashrc

USER ${USER}
RUN python -m pip install --upgrade pip --user
RUN python -m pip install gym==0.10.11
RUN python -m pip install ipdb==0.11
RUN python -m pip install matplotlib==2.1.2
RUN python -m pip install pyyaml==3.12
RUN python -m pip install tqdm==4.25.0
RUN python -m pip install wheel==0.30.0
RUN python -m pip install scipy==1.0.0

# RUN python -m pip install cffi==1.15.0
# RUN python -m pip install Cython==0.29.26
# RUN python -m pip install glfw==2.5.0
RUN python -m pip install imageio==2.9.0
# RUN python -m pip install lockfile==0.12.2
# RUN python -m pip install pycparser==2.21
RUN python -m pip install -U mujoco-py==1.50.1

ENV NVIDIA_VISIBLE_DEVICES ${NVIDIA_VISIBLE_DEVICES:-all}
ENV NVIDIA_DRIVER_CAPABILITIES ${NVIDIA_DRIVER_CAPABILITIES:+$NVIDIA_DRIVER_CAPABILITIES,}graphics

WORKDIR /home/${USER}/

# RUN git clone https://github.com/google-research/pddm.git
# RUN python -m pip install -e ${HOME}/pddm/.

# sudo docker run --env="DISPLAY" --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" -v=/home/sattyorin/pddm_docker:/home/sattyorin/pddm --gpus all --privileged --runtime=nvidia -it pddm:1.0 bash

# pddm/pddm/utils/helper_funcs.py
# rc('font', **{'family': 'serif', 'serif': ['Palatino']}) #comment out

# pddm/pddm/script/train.py
# # with tf.Session(config=get_gpu_config(args.use_gpu, args.gpu_frac)) as sess:
# configuration = get_gpu_config(args.use_gpu, args.gpu_frac)
# configuration.gpu_options.allow_growth = True
# with tf.Session(config=configuration) as sess:
