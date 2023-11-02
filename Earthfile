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
    RUN make install 

wg-dynamic-server-container:
    FROM +make-all
    #TODO: setup wg0 interface... 
    EXPOSE 51900
    ENTRYPOINT ["wg-dynamic-server", "--leasetime", "10", "wg0"]
    SAVE IMAGE wg-dynamic-server:local-earthly-testing
    
wg-dynamic-client-container:
    FROM +make-all
    #TODO: setup wg0 interface... 
    EXPOSE 51900
    ENTRYPOINT ["wg-dynamic-client", "wg0"]
    SAVE IMAGE wg-dynamic-client:local-earthly-testing


tests:
    FROM earthly/dind:ubuntu
    ENV DEBIAN_FRONTEND=noninteractive
    RUN apt-get update -y && apt-get install iproute2 iputils-ping -y 
    COPY docker-compose.yaml ./
    WITH DOCKER --compose docker-compose.yaml \
        --load wg-dynamic-server:local-earthly-testing=+wg-dynamic-server-container \
        --load wg-dynamic-client:local-earthly-testing=+wg-dynamic-client-container

        RUN docker run --network=default_wg-dynamic-net ubuntu:latest \
            echo "just running an ubuntu:latest container on the wg-dynamic network."; \
            echo "will need actual testing here..."; \
        
        docker inspect default_wg-dynamic-net

    END
