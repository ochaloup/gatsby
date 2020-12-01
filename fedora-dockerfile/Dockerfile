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

RUN echo 'PATH=$PATH:$HOME/node_modules/.bin/' >> /home/gatsby/.bashrc

RUN mkdir -p .config/gatsby
COPY config.json .config/gatsby/

# TODO: parametrize this
RUN git config --global user.email "ochaloup@redhat.com"  &&\
    git config --global user.name "Ondra Chaloupka"

# building gatsby blog at $HOME/myblog
# when mounted with a mount from system it can be copied to the mounted directory
ARG BLOGDIR=myblog

### lumen starter : sources
# https://medium.com/swlh/how-to-build-a-jamstack-blog-site-with-gatsby-js-lumin-and-netlify-2562eb1eeb38
# https://github.com/alxshelepenok/gatsby-starter-lumen
RUN /home/gatsby/node_modules/.bin/gatsby new "$BLOGDIR" https://github.com/alxshelepenok/gatsby-starter-lumen
## novela : sources
# https://github.com/narative/gatsby-theme-novela#getting-started-with-gatsby-starter-novela
# RUN /home/gatsby/node_modules/.bin/gatsby new "$BLOGDIR" https://github.com/narative/gatsby-starter-novela

RUN cd $BLOGDIR && yarn upgrade

## installing the AsciiDoc transformer to write articles with AsciiDoc (https://www.gatsbyjs.org/packages/gatsby-transformer-asciidoc/)
RUN npm install --save gatsby-transformer-asciidoc

RUN npm install --save bluebird &&\
    npm install --save gatsby-link &&\
    npm install --save classnames

# BUILD AS
# docker build . -t gatsby-build --build-arg USER_ID=$(id -u)
# RUN AS
# mkdir $HOME/my-testing/gatsby/sources
# docker run -it --user $(id -u) -p 8000:8000 -v $HOME/my-testing/gatsby/sources:/home/gatsby/sources --name gatsby gatsby-build
# podman run -it --user $(id -u)  --userns keep-id -p 8000:8000 --mount type=bind,source=$HOME/my-testing/gatsby/sources,target=/home/gatsby/sources --name gatsby gatsby-build

# TROUBLE #1: how to change sources and permission denied
## -- if your user has not has defined the same uid number then it's probably the only solution to nuked the `blog` directory with
## chmod a+rwx -R $HOME/my-testing/gatsby/sources
## docker exec -u 0 -it <docker_image> sh -c 'chmod a+rwx -R /home/gatsby/sources'

# HELPER: clean env
## docker rm $(docker ps -q -f status=exited)

# NEXT STEPS: myblog to the shared volume
## mv ~/myblog ~/source/myblog
# TROUBLE #2: podman error 'mv: setting attribute 'security.selinux' for 'security.selinux': Permission denied'
# it's just warning, ignore it :-)

# NEXT STEPS #3: develop
## 

# TODO: what to do next? :)
