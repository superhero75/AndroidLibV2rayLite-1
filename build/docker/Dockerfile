FROM ubuntu:18.04

# update sys and install build java8 required
RUN apt-get update && apt-get -y upgrade && \
    apt-get -y install openjdk-8-jdk wget make git unzip && \
    apt-get autoremove && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

#go installation
#RUN wget -P /tmp https://dl.google.com/go/go1.13.8.linux-amd64.tar.gz
#RUN tar -C /usr/local -xzf /tmp/go1.13.8.linux-amd64.tar.gz
#RUN rm /tmp/go1.13.5.linux-amd64.tar.gz

RUN wget https://dl.google.com/go/go1.15.2.linux-amd64.tar.gz
RUN tar -xvf go1.15.2.linux-amd64.tar.gz
RUN mv go /usr/local
RUN chmod -R 777 $HOME && mkdir $HOME/go/
RUN rm /go1.15.2.linux-amd64.tar.gz

ENV GOROOT=/usr/local/go
ENV GOPATH=$HOME/go
ENV PATH=$GOPATH/bin:$GOROOT/bin:$PATH

#ENV GOPATH /go
#RUN mkdir /opt/go

#ENV PATH=$PATH:/usr/local/go/bin:~/go/bin
ENV GO111MODULE=off

#ENV GOROOT="/usr/local/go"
#ENV GOBIN="$GOROOT/bin"
#ENV GOPATH="/usr/local"
ENV GOPROXY=direct

#RUN mkdir -p "$GOPATH/src" "$GOPATH/bin" && chmod -R 777 "$GOPATH"
WORKDIR /tmp
RUN git clone https://github.com/superhero75/AndroidLibV2rayLite-1.git
RUN mv /tmp/AndroidLibV2rayLite-1/AndroidLibV2rayLite $GOROOT/src
RUN rm -rf /tmp/AndroidLibV2rayLite-1
WORKDIR $GOPATH/src/AndroidLibV2rayLite
RUN go get golang.org/x/mobile/cmd/gomobile
RUN go get -u github.com/golang/protobuf/protoc-gen-go/...
RUN go get -u golang.org/x/mobile/cmd/...
RUN go get -u github.com/jteeuwen/go-bindata/...
RUN go get -u -insecure v2ray.com/core/...

#android sdk ndk installation
RUN mkdir -p /opt/android-sdk-linux && mkdir -p ~/.android && touch ~/.android/repositories.cfg
WORKDIR /opt

ENV ANDROID_HOME /opt/android-sdk-linux
ENV PATH ${PATH}:${ANDROID_HOME}/tools/bin:${ANDROID_HOME}/platform-tools:${PATH}:${ANDROID_HOME}/tools
ENV ANDROID_NDK /opt/android-ndk-linux
ENV ANDROID_NDK_HOME /opt/android-ndk-linux

RUN cd /opt/android-sdk-linux && \
	wget -q --output-document=sdk-tools.zip https://dl.google.com/android/repository/sdk-tools-linux-4333796.zip && \
	unzip sdk-tools.zip && \
	rm -f sdk-tools.zip && \
	echo y | sdkmanager "build-tools;27.0.3" "platforms;android-27" && \
	echo y | sdkmanager "build-tools;29.0.3" "platforms;android-29" && \
	echo y | sdkmanager "build-tools;28.0.3" "platforms;android-28" && \
	echo y | sdkmanager "extras;android;m2repository" "extras;google;m2repository" "extras;google;google_play_services" && \
	sdkmanager "cmake;3.6.4111459"
RUN wget -q --output-document=android-ndk.zip https://dl.google.com/android/repository/android-ndk-r21-linux-x86_64.zip && \
	unzip android-ndk.zip && \
	rm -f android-ndk.zip && \
	mv android-ndk-r21 android-ndk-linux

WORKDIR $GOROOT/src/AndroidLibV2rayLite/
RUN bash gen_assets.sh

WORKDIR $GOROOT/src/AndroidLibV2rayLite/shippedBinarys
RUN make shippedBinary

WORKDIR $GOROOT/src/AndroidLibV2rayLite
RUN gomobile init && gomobile bind -v  -tags json .

#WORKDIR /home/myapp
#CMD ["/bin/bash","-vx","build-in-docker.sh","data","dep"]
