FROM opensuse/tumbleweed
LABEL maintainer="mizux.dev@gmail.com"

# Install/Configure nodejs, npm, yarn
ENV NPM_CONFIG_PREFIX=/home/node/.npm-global
ENV PATH=$PATH:/home/node/.npm-global/bin

RUN zypper up -y \
&& zypper install -y nodejs12 npm12 \
&& zypper clean -a

# Global Dependencies
RUN npm install --global yarn
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
