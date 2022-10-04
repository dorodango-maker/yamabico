FROM ubuntu:20.04

ENV TZ=Asia/Tokyo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Prerequisites
RUN apt update && apt install -y curl git unzip xz-utils zip libglu1-mesa openjdk-8-jdk wget
RUN apt update && apt install -y gnupg2

WORKDIR /home/developer

# Prepare Android directories and system variables
RUN mkdir -p Android/sdk
ENV ANDROID_SDK_ROOT /home/developer/Android/sdk
RUN mkdir -p .android && touch .android/repositories.cfg

# Setup Android SDK
RUN wget -O sdk-tools.zip https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip
RUN unzip sdk-tools.zip && rm sdk-tools.zip
RUN mv tools Android/sdk/tools
RUN cd Android/sdk/tools/bin && yes | ./sdkmanager --licenses
RUN cd Android/sdk/tools/bin && ./sdkmanager "build-tools;29.0.2" "patcher;v4" "platform-tools" "platforms;android-29" "sources;android-29"
ENV PATH "$PATH:/home/developer/Android/sdk/platform-tools"

# Download Flutter SDK
# fvm1を使わないなら
# RUN git clone https://github.com/flutter/flutter.git
# ENV PATH "$PATH:/home/developer/flutter/bin"

# Run basic check to download Dark SDK
# RUN flutter doctor

ARG PATH=/root/.pub-cache/bin:$PATH
ARG FLUTTER_VERSION=3.3.3


RUN wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -
RUN wget -qO- https://storage.googleapis.com/download.dartlang.org/linux/debian/dart_stable.list > /etc/apt/sources.list.d/dart_stable.list
RUN apt update && apt install -y dart
RUN dart pub global activate melos --verbose && \
    dart pub global activate fvm --verbose && \
    fvm doctor --verbose && \
    fvm install $FLUTTER_VERSION --verbose && \
    fvm use --force $FLUTTER_VERSION --verbose && \
    fvm flutter config --enable-web --enable-linux-desktop --enable-macos-desktop --enable-windows-desktop --enable-android --enable-ios --enable-fuchsia && \
    # fvm flutter precache --verbose && \
    fvm flutter doctor --verbose

# Set paths
ENV FVM_ROOT=/root/.pub-cache
ENV PATH $FVM_ROOT/bin:$PATH