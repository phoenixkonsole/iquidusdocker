FROM ubuntu:16.04
LABEL Description="This image is a clean build of paparad from a paparab branch"

RUN apt-get update
RUN apt-get install --yes software-properties-common
RUN add-apt-repository --yes ppa:bitcoin/bitcoin

RUN apt-get update && \
    apt-get install -y libunbound-dev build-essential libtool autotools-dev autoconf libssl-dev git-core libboost-all-dev libdb4.8-dev libdb4.8++-dev libminiupnpc-dev nano pkg-config && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN git clone https://github.com/phoenixkonsole/papara/ /opt/papara

RUN cd /opt/papara && \
    ./autogen.sh && \
    ./configure --with-miniupnpc --enable-upnp-default --disable-tests --without-gui --without-qrcode
    
RUN cd /opt/papara && \
    make -j2

RUN cp /opt/papara/src/paparad /usr/local/bin/ && \
    cp /opt/papara/src/papara-cli /usr/local/bin/

RUN groupadd -r papara && useradd -r -m -g papara papara
ENV papara_DATA /data
RUN mkdir $papara_DATA
ADD . /data
RUN chown -R papara:papara $papara_DATA
RUN ln -s $papara_DATA /home/papara/.papara
USER root
VOLUME /data
WORKDIR /data
EXPOSE 19001 19011
CMD ["make start"]
