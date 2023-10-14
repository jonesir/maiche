#-------------------------------------------------------------------------------------------------------------
# Flutter Dev Container - Lucas Hilleshein dos Santos.
# Licensed under the MIT License.
# See https://github.com/lucashilles/flutter-dev-container/blob/master/LICENSE for license information.
#-------------------------------------------------------------------------------------------------------------

FROM ubuntu:latest

#Locale
ENV LANG C.UTF-8



# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=developer
ARG USER_UID=1000
ARG USER_GID=$USER_UID

#
# Install needed packages, setup user anda clean up.
RUN  apt update \
	&& apt install -y sudo \
	&& apt install -y openjdk-18-jdk --no-install-recommends \
	&& apt install -y wget curl git xz-utils zip unzip --no-install-recommends 
	
	# Clean Up
RUN	apt-get autoremove -y \
	&& apt-get clean -y \
	&& rm -rf /var/lib/apt/lists/* 
	# Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
	# [Optional] Add sudo support for the non-root user
# RUN	groupadd --gid 1888 developer \
# 	&& useradd -s /bin/bash --uid $USER_UID --gid 1888 -m developer \
# 	&& echo developer ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/developer \
# 	&& chmod 0440 /etc/sudoers.d/developer \
# 	&& su developer \
#     && whoami \
# 	&& cd /home/developer

RUN useradd -ms /bin/bash $USERNAME && \
    usermod -aG sudo $USERNAME
# New added for disable sudo password
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Set as default user
USER $USERNAME
WORKDIR /home/$USERNAME




#
# Android SDK
# https://developer.android.com/studio#downloads
ENV ANDROID_PLATFORM_VERSION=34
ENV ANDROID_BUILD_TOOLS_VERSION=34.0.0
ENV ANDROID_HOME=~/android-sdk-linux
ENV ANDROID_SDK_ROOT="$ANDROID_HOME"
ENV PATH=${PATH}:${ANDROID_HOME}/cmdline-tools/cmdline-tools/bin:${ANDROID_HOME}/platform-tools:${ANDROID_HOME}/emulator

#
# Flutter SDK
# https://flutter.dev/docs/development/tools/sdk/releases?tab=linux
# Make sure to use the needed channel and version for this.
ENV FLUTTER_HOME=~/flutter
ENV PATH=${PATH}:${FLUTTER_HOME}/bin


#
# Android SDK	
RUN curl -C - --output android-sdk-tools.zip https://dl.google.com/android/repository/commandlinetools-linux-10406996_latest.zip \
	&& mkdir -p ${ANDROID_HOME}/ \
	&& unzip -q android-sdk-tools.zip -d ${ANDROID_HOME}/cmdline-tools/ \
	&& rm android-sdk-tools.zip \
	&& yes | sdkmanager --licenses \
    && touch ~/.android/repositories.cfg \
	&& sdkmanager platform-tools \
	# && sdkmanager emulator \
	&& sdkmanager "platforms;android-${ANDROID_PLATFORM_VERSION}" "build-tools;${ANDROID_BUILD_TOOLS_VERSION}" \
	&& sdkmanager --install "cmdline-tools;latest" 
# create emulator android	
# RUN  sdkmanager "system-images;android-${ANDROID_PLATFORM_VERSION};google_apis;x86_64" \
# 	 && avdmanager create avd -n Android${ANDROID_PLATFORM_VERSION} -k "system-images;android-${ANDROID_PLATFORM_VERSION};google_apis;x86_64"


#
# Flutter SDK
RUN whoami
RUN curl -C - --output flutter.tar.xz https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_3.13.7-stable.tar.xz \
	&& tar -xf flutter.tar.xz -C ~ \
	&& rm flutter.tar.xz \
    && echo $PATH \
    && pwd \
    && ls ~/flutter \
    && ls ~/flutter/bin \
	&& ~/flutter/bin/flutter config --android-sdk "${ANDROID_SDK_ROOT}" \
	&& yes | ~/flutter/bin/flutter doctor --android-licenses \
	&& ~/flutter/bin/flutter config --no-analytics \
	&& ~/flutter/bin/flutter update-packages