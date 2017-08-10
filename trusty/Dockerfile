FROM ubuntu:trusty

RUN apt-get update && apt-get upgrade -y && apt-get install -y build-essential checkinstall wget debhelper ruby1.9 tcl
RUN gem install fpm

RUN mkdir -p /fpm/usr
RUN mkdir -p /fpm/etc/init.d
RUN mkdir -p /fpm/etc/redis

RUN wget http://download.redis.io/releases/redis-3.2.4.tar.gz
RUN tar -xzvf redis-3.2.4.tar.gz
RUN cd redis-3.2.4 && make
RUN cd redis-3.2.4 && make test
COPY redis.conf /fpm/etc/redis/redis.conf
COPY init-script /fpm/etc/init.d/redis-server
COPY before-install /tmp/before-install
COPY before-remove /tmp/before-remove
COPY after-remove /tmp/after-remove

RUN cd redis-3.2.4 && PREFIX=/fpm/usr make install 

RUN mkdir /out

RUN ls -al /fpm/usr/bin

WORKDIR /fpm

CMD /bin/bash -c "fpm -s dir -t deb -n redis-tools -v 3.2.4-trusty-talis \
    usr/bin/redis-benchmark \
    usr/bin/redis-check-aof \
    usr/bin/redis-check-rdb \
    usr/bin/redis-cli && \
    fpm -s dir -t deb -n redis-server  \
    -d \"redis-tools (=3.2.4-trusty-talis)\" \
    --before-install /tmp/before-install \
    --before-remove /tmp/before-remove \
    --after-remove /tmp/after-remove \
    -v 3.2.4-trusty-talis usr/bin/redis-server etc && \
    cp /fpm/*.deb /out"