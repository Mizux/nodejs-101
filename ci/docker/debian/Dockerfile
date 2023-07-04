FROM debian:latest AS env

# Install/Configure nodejs, npm, yarn
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global
ENV PATH=$PATH:/home/node/.npm-global/bin

RUN apt-get update -q \
&& apt-get install -yq nodejs npm \
&& apt-get clean \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Global Dependencies
RUN npm install --global yarn
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
