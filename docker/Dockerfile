ARG ROS_DISTRO=humble
#ARG SUBNET='10.1.1.0'

#FROM ros:$ROS_DISTRO-ros-base
FROM osrf/ros:$ROS_DISTRO-desktop-full

SHELL ["/bin/bash", "-c"]
ENV DEBIAN_FRONTEND=noninteractive

# Switch to cyclonedds
RUN apt update \
  && apt install -y \
  ros-${ROS_DISTRO}-rmw-cyclonedds-cpp \
  && rm -rf /var/lib/apt/lists/*

ENV RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
#ENV CYCLONEDDS_URI="<CycloneDDS><Domain><General><AllowMulticast>true</AllowMulticast><Interfaces><NetworkInterface address=$SUBNET /></Interfaces></General></Domain></CycloneDDS>"

# Increase buffer size as recommended by rmw_cyclonedds_cpp
RUN echo 'net.core.rmem_max=2147483647' >> /etc/sysctl.conf
RUN echo 'net.core.rmem_default=8388608' >> /etc/sysctl.conf

# Deps
RUN apt-get update \
  && apt-get install -y \
  # build-essential \
  # libeigen3-dev \
  # libjsoncpp-dev \
  # libspdlog-dev \
  # libcurl4-openssl-dev \
  # cmake \
  # python3-colcon-common-extensions \
  # ros-${ROS_DISTRO}-pcl-ros \
  # ros-${ROS_DISTRO}-tf2-eigen \
  # ros-${ROS_DISTRO}-rviz2 \
  ros-${ROS_DISTRO}-image-pipeline \
  && rm -rf /var/lib/apt/lists/*

# Setup Workspace
COPY ../src /ros2_ws/src
WORKDIR /ros2_ws
RUN source /opt/ros/${ROS_DISTRO}/setup.bash \
 # && rosdep update \
 # && rosdep install -i --from-path src --rosdistro ${ROS_DISTRO} -y \
 # && rosdep install -i --from-path src --rosdistro ${ROS_DISTRO} -y -r \
 && colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=Release \
 && echo 'source /opt/ros/${ROS_DISTRO}/setup.bash' >> /root/.bashrc \
 && echo 'source /ros2_ws/install/setup.bash' >> /root/.bashrc \
 && echo 'source /ros2_ws/install/setup.bash' >> /ros_entrypoint.sh

RUN sed -i '/exec "$@"/d' /ros_entrypoint.sh \
 && echo 'exec "$@"' >> /ros_entrypoint.sh

ENTRYPOINT [ "/ros_entrypoint.sh" ]

ENV DEBIAN_FRONTEND=dialog