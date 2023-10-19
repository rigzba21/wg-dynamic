VERSION 0.7
FROM ubuntu:latest

install-dependencies:
    RUN apt-get update -y && \
        apt-get install -y \
        build-essential \
        nftables \ #libnml dependency 
        libmnl-dev #https://netfilter.org/projects/libmnl/index.html 

make-all:
    FROM +install-dependencies
    COPY . . 
    RUN make all 
