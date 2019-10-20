FROM centos:latest
LABEL maintainer="mizux.dev@gmail.com"

# Install/Configure nodejs, npm, yarn
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global
ENV PATH=$PATH:/home/node/.npm-global/bin

RUN yum -y update \
&& (curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | tee /etc/yum.repos.d/yarn.repo) \
&& yum -y install nodejs npm yarn \
&& yum clean all \
&& rm -rf /var/cache/yum

# Global Dependencies
#RUN yarn global add @vue/cli

# Create app directory
WORKDIR /home/node/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install

# Bundle app sources
COPY . .

EXPOSE 8080
CMD [ "yarn", "start" ]
