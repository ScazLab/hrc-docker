FROM ros:indigo-robot
FROM ros:indigo-perception

SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND noninteractive

# Set up work directory
RUN useradd dev
RUN mkdir /home/dev && chown -R dev: /home/dev
ENV HOME "/home/dev"
WORKDIR   /home/dev

# Add manually Eigen3 and g++-4.9
RUN apt-get update && apt-get -y install software-properties-common libeigen3-dev
RUN add-apt-repository ppa:ubuntu-toolchain-r/test
RUN apt-get update && apt-get -y install gcc-4.9 g++-4.9
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-4.9 50
RUN update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-4.9 50

# Avoid ERROR: invoke-rc.d: policy-rc.d denied execution of start.
# RUN sed -i "s/^exit 101$/exit 0/" /usr/sbin/policy-rc.d
RUN apt-get -y install git-core python-argparse python-wstool python-vcstools python-rosdep \
                       ros-indigo-control-msgs ros-indigo-joystick-drivers python-catkin-tools

RUN rosdep update
RUN mkdir -p ~/ros_ws/src && cd ~/ros_ws/src \
    && wstool init . \
    && wstool merge https://raw.githubusercontent.com/RethinkRobotics/baxter/master/baxter_sdk.rosinstall \
    && wstool update
RUN cd ~/ros_ws/src \
    && git clone https://github.com/ros/xacro.git \
    && git clone https://github.com/ros/roslint.git
RUN echo `which catkin`
RUN cd ~/ros_ws && catkin init
RUN cd ~/ros_ws && source /opt/ros/$ROS_DISTRO/setup.bash && catkin build

# ENV ROS_MASTER_URI="http://baxter.local:11311"

COPY ./ros-entrypoint.sh /bin/
RUN ["chmod", "+x", "/bin/ros-entrypoint.sh"]
ENTRYPOINT ["/bin/ros-entrypoint.sh"]

RUN chown -R dev: /home/dev
USER dev
CMD ["/bin/bash"]
