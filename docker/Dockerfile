FROM ubuntu
WORKDIR /
RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update -y && \
    apt-get install awscli -y
CMD ["/bin/bash"]