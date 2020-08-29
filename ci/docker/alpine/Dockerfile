FROM alpine:latest AS env
LABEL maintainer="mizux.dev@gmail.com"

# Install/Configure nodejs, npm, yarn
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global
ENV PATH=$PATH:/home/node/.npm-global/bin

RUN apk add --no-cache nodejs npm yarn

# Global Dependencies
#RUN yarn global add @vue/cli

FROM env AS devel
# Create app directory
WORKDIR /home/node/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./
RUN yarn install

# Bundle app sources
COPY . .

FROM devel AS build
RUN yarn build

EXPOSE 8080
CMD [ "yarn", "start" ]
