FROM fedora:32

# RUN dnf update -y

RUN dnf install -y git curl gcc-c++ make vips

# https://tecadmin.net/install-latest-nodejs-on-fedora/
RUN curl -sL https://rpm.nodesource.com/setup_14.x | sudo -E bash -
RUN dnf install -y nodejs
RUN node --version &&\
    npm --version
RUN curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
RUN dnf install -y yarn

EXPOSE 8080

RUN groupadd -g 17550 gatsby &&\
    useradd gatsby -u 17550 -g 17550
USER gatsby
WORKDIR /home/gatsby

RUN npm install node-gyp sharp &&\
    npm install gatsby-cli

RUN echo 'PATH=$PATH:node_modules/.bin/' >> /home/gatsby/.bashrc

RUN mkdir -p .config/gatsby
COPY config.json .config/gatsby/

# RUN gatsby new myblog https://github.com/alxshelepenok/gatsby-starter-lumen
# build as
# docker build . -t gatsby-build
# run as
# docker run -it -p 8080:8080 -v ~/myblog:/home/gatsby/myblog gatsby-build
# docker exec -u 0 -it <docker_image> sh -c 'chmod uog+rwx -R /home/gatsby/myblog'
