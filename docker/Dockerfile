FROM node:buster-slim AS base

EXPOSE 8080

RUN apt-get update && \
    apt-get install -y zip && \
    rm -rf /var/lib/apt/lists/*

COPY start-npm /usr/local/bin/start-npm

USER node

WORKDIR /home/node/mainsail

ENTRYPOINT ["start-npm"]
