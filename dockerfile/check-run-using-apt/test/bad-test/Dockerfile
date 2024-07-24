FROM ubuntu:latest

ENV enivronment=debian

RUN apt-get update && \
    apt install -y --no-install-recommends curl
    
CMD ["curl", "--version"]