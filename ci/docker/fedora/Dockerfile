FROM fedora:latest
LABEL maintainer="mizux.dev@gmail.com"

# Install/Configure nodejs, npm, yarn
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global
ENV PATH=$PATH:/home/node/.npm-global/bin

RUN dnf -y update \
&& (curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | tee /etc/yum.repos.d/yarn.repo) \
&& dnf -y install nodejs npm yarn \
&& dnf clean all

# Global Dependencies
#RUN yarn global add @vue/cli

# Create app directory
WORKDIR /home/node/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN yarn install

# Bundle app sources
COPY . .

EXPOSE 8080
CMD [ "yarn", "start" ]
