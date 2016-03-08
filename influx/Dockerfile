FROM golang:1.5

ENV DEBIAN_FRONTEND noninteractive
ENV GOPATH /home/gocodez

#install docker and go build tools
RUN echo 'deb http://http.debian.net/debian wheezy-backports main' >> /etc/apt/sources.list \
    && apt-get update \
    && apt-get install -y -t wheezy-backports linux-image-amd64 \
       mercurial bzr protobuf-compiler flex bison libgflags-dev libsnappy-dev\
       valgrind g++ make autoconf libtool libz-dev libbz2-dev zlib1g-dev curl \
       rpm build-essential git wget gawk \
    && curl -sSL https://get.docker.io/ | sh

#checkout InfluxDB version 0.10.2
RUN mkdir -p $GOPATH/src/github.com/influxdb && \
 cd $GOPATH/src/github.com/influxdb && \
 git clone https://github.com/influxdb/influxdb.git && \
 cd influxdb && git checkout tags/v0.10.2

RUN go get github.com/dgryski/go-bits && go get github.com/dgryski/go-bitstream && \
    go get github.com/influxdb/usage-client/v1 && go get github.com/jwilder/encoding/simple8b && \
    go get github.com/BurntSushi/toml && go get github.com/bmizerany/pat && go get github.com/boltdb/bolt && \
    go get github.com/gogo/protobuf/proto && go get github.com/golang/snappy && go get github.com/hashicorp/raft && \
    go get github.com/hashicorp/raft-boltdb && go get github.com/kimor79/gollectd && go get github.com/rakyll/statik/fs && \
    go get golang.org/x/crypto/bcrypt && go get gopkg.in/fatih/pool.v2

WORKDIR $GOPATH/src/github.com/influxdb/influxdb

#configure and build binary as a static binary
#ENV GO_BUILD_OPTIONS --ldflags '-s -extldflags "-static"'
RUN CGO_ENABLED=0 go build -a --installsuffix cgo --ldflags='-s -extldflags "-static"' -o influxd ./cmd/influxd
RUN CGO_ENABLED=0 go build -a --installsuffix cgo --ldflags='-s -extldflags "-static"' -o influx_tsm ./cmd/influx_tsm

#RUN file ${GOPATH}/src/github.com/influxdb/influxdb/influxd
#ADD config.toml $GOPATH/src/github.com/influxdb/influxdb/config.toml
#ADD run_influxdb $GOPATH/src/github.com/influxdb/influxdb/run_influxdb
#ADD Dockerfile.run $GOPATH/src/github.com/influxdb/influxdb/Dockerfile
#ADD ${GOPATH}/src/github.com/influxdb/influxdb/influxd $GOPATH/src/github.com/influxdb/influxdb/influxdb

RUN mkdir -p /opt
ADD config.toml /opt/config.toml
ADD Dockerfile.run /opt/Dockerfile
RUN cp ${GOPATH}/src/github.com/influxdb/influxdb/influx_tsm /opt/influx_tsm
RUN cp ${GOPATH}/src/github.com/influxdb/influxdb/influxd /opt/influxd
WORKDIR /opt
# Export the WORKDIR as a tar stream
CMD tar -cf - .
