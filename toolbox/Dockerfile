FROM ubuntu:latest

LABEL Name=toolbox Version=0.0.3 MAINTAINER="Mohammad Nofal <mohmd.nofal@gmail.com>"

RUN apt-get -y update && \
apt-get install -yq --no-install-recommends \ 
openssh-client curl dnsutils tcpdump && apt-get clean \
&& rm -rf /var/lib/apt/lists/*

EXPOSE 22

CMD ["/bin/bash"]
