FROM fedora:32

RUN dnf update -y

RUN dnf install -y git curl gcc-c++ make vips vim

# https://tecadmin.net/install-latest-nodejs-on-fedora/
RUN curl -sL https://rpm.nodesource.com/setup_14.x | sudo -E bash -
RUN dnf install -y nodejs
RUN node --version &&\
    npm --version
RUN curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
RUN dnf install -y yarn
RUN dnf install -y libXtst

EXPOSE 8080

ARG USER_ID
RUN useradd gatsby -u $USER_ID
USER gatsby
WORKDIR /home/gatsby

RUN npm install node-gyp sharp &&\
    npm install gatsby-cli
RUN npm install --save gatsby-transformer-asciidoc

RUN echo 'PATH=$PATH:$HOME/node_modules/.bin/' >> /home/gatsby/.bashrc

RUN mkdir -p .config/gatsby
COPY config.json .config/gatsby/

# TODO: parametrize this
RUN git config --global user.email "ochaloup@redhat.com"  &&\
    git config --global user.name "Ondra Chaloupka"

# building gatsby blog at $HOME/myblog
# when mounted with a mount from system it can be copied to the mounted directory
ARG BLOGDIR=myblog
RUN /home/gatsby/node_modules/.bin/gatsby new "$BLOGDIR" https://github.com/alxshelepenok/gatsby-starter-lumen

# BUILD AS
# docker build . -t gatsby-build --build-arg USER_ID=$(id -u)
# RUN AS
# mkdir $HOME/my-testing/gatsby/sources
# docker run -it --user $(id -u) -p 8080:8080 -v $HOME/my-testing/gatsby/sources:/home/gatsby/sources --name gatsby gatsby-build

# NOTES
## -- if your user has not has defined the same uid number then it's probably the only solution to nuked the `blog` directory with
## chmod a+rwx -R $HOME/my-testing/gatsby/sources
## docker exec -u 0 -it <docker_image> sh -c 'chmod a+rwx -R /home/gatsby/sources'
## -- only when necessary to clean the environment
## docker rm $(docker ps -q -f status=exited)

# AFTER
## there could be already ~/myblog generated in the docker image and only doing like `mv ~/myblog ~/sources/myblog` is necessary
## gatsby new myblog https://github.com/alxshelepenok/gatsby-starter-lumen
# cd ~/sources
# cd myblog
# yarn upgrade
# npm install --save gatsby-transformer-asciidoc
## see details at: https://www.gatsbyjs.org/packages/gatsby-transformer-asciidoc/

# TODO: what to do next? :)
