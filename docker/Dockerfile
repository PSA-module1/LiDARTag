# 1. ros:melodic을 베이스 이미지로 설정
FROM ros:melodic-ros-base


ARG DEBIAN_FRONTEND=noninteractive

# Install basic apt tools
RUN apt update && apt upgrade -y && apt install -y \
    # Replace python-rosdep with python3-rosdep if running noetic
    vim wget zsh git python3-pip python3-catkin-tools && \
    # create catkin_ws
    mkdir -p /root/catkin_ws/src

# Install oh-my-zsh (if don't want can remove this code block and subsequent sourcing of ros .setup for .zshrc)
RUN sh -c "$(wget -O- https://github.com/deluan/zsh-in-docker/releases/download/v1.1.2/zsh-in-docker.sh)" -- \
    -t ys \
    -p git \
	-p z \
    -p https://github.com/zsh-users/zsh-autosuggestions \
    -p https://github.com/zsh-users/zsh-completions \
    -p https://github.com/zsh-users/zsh-syntax-highlighting

# 2. cmake 버전 업그레이드
RUN mkdir -p /root/lidartag_lib && cd /root/lidartag_lib && \
    wget https://github.com/Kitware/CMake/releases/download/v3.18.2/cmake-3.18.2.tar.gz && \
    tar -xvf cmake-3.18.2.tar.gz && \
    cd cmake-3.18.2 && \
    ./bootstrap && \
    make && \
    make install

# 3. nlopt 라이브러리 설치를 위한 기본 세팅
RUN apt update && \
    apt install -y python3 python3-pip && \
    pip3 install numpy && \
    pip3 install swig

# 4. nlopt 라이브러리 설치
RUN cd /root/lidartag_lib && \
    git clone https://github.com/stevengj/nlopt.git && \
    cd nlopt && \
    mkdir build && \
    cd build && \
    cmake .. -DNLOPT_OCTAVE=Off -DNLOPT_MATLAB=Off -DNLOPT_GUILE=Off && \
    make && \
    make install

# 5. tbb 라이브러리 설치
RUN cd /root && \
    git clone https://github.com/wjakob/tbb && \
    cd tbb && \
    cd build && \
    cmake .. && \
    cmake --build . --config Release -- -j 6 && \
    make install

# 6. 연관된 ros 패키지 설치
RUN apt update && \
    apt install -y ros-melodic-tf && \
    apt install -y ros-melodic-velodyne-pointcloud
    RUN . /opt/ros/${ROS_DISTRO}/setup.sh && \
    echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> ~/.bashrc && \
    echo "source /opt/ros/${ROS_DISTRO}/setup.zsh" >> ~/.zshrc

# 7. clone lidartag_msgs
RUN cd /root/catkin_ws/src && \
    git clone https://github.com/UMich-BipedLab/LiDARTag_msgs.git

# Set up entrypoint
WORKDIR /root
COPY entrypoint.sh /entrypoint.sh
RUN sudo chmod +x /entrypoint.sh
# Set the default command to zsh
ENTRYPOINT [ "/entrypoint.sh" ] 
CMD zsh
