FROM ubuntu:20.04   as   build

ARG assistGitUrl="https://github.com/648540858/wvp-pro-assist.git"
ARG zlmGitUrl="https://github.com/ZLMediaKit/ZLMediaKit.git"

COPY sources.list /etc/apt/sources.list

RUN export DEBIAN_FRONTEND=noninteractive &&\
        apt-get update && \
        apt-get install -y --no-install-recommends openjdk-11-jre git libssl-dev libsdl-dev libavcodec-dev libavutil-dev ffmpeg maven build-essential \
        cmake ca-certificates openssl ffmpeg &&\
        mkdir -p /opt/assist/config /opt/assist/heapdump /opt/media/www/record
# 构建wvp-pro-assist, 录像助手
RUN cd /home && \
        git clone "${assistGitUrl}" && cd wvp-pro-assist &&\
        git checkout 7360de7ff7570acfd3af46a87b49588170b4545a

RUN cd /home/wvp-pro-assist && \
        mvn clean package -Dmaven.test.skip=true && \
        cp /home/wvp-pro-assist/target/*.jar /opt/assist/ 
# 构建ZLMediaKit
RUN cd /home && \
        git clone --depth=1 "${zlmGitUrl}" && cd ZLMediaKit && \
        git checkout 44f4e9acd434e5549276332aa454be18844db3a8

RUN cd /home/ZLMediaKit && \
        git submodule update --init --recursive && \
        mkdir -p build release/linux/Release/ &&\
        cd build && \
        cmake -DCMAKE_BUILD_TYPE=Release .. && \
        make -j4 && \
        rm -rf ../release/linux/Release/config.ini && \
        cp -r ../release/linux/Release/* /opt/media


FROM ubuntu:20.04

ENV LC_ALL zh_CN.UTF-8

COPY sources.list /etc/apt/sources.list

RUN export DEBIAN_FRONTEND=noninteractive &&\
        apt-get update && \
        apt-get install -y --fix-missing --no-install-recommends openjdk-11-jre ca-certificates openssl ffmpeg language-pack-zh-hans && \
        apt-get autoremove -y && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*dic

COPY --from=build /opt /opt
WORKDIR /config
CMD ["sh", "run-zlm.sh"]