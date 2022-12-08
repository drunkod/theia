FROM quay.io/centos/centos:stream8

ARG BUILD_DATE
LABEL org.label-schema.name="simplicite-vscode" \
      org.label-schema.vendor="VSCode server / Simplicite Software" \
      org.label-schema.build-date="$BUILD_DATE" \
      org.label-schema.license="NONE" \
      org.opencontainers.image.ref.name="simplicite-vscode" \
      org.opencontainers.image.title="VSCode server IDE for Simplicite" \
      org.opencontainers.image.description="VSCode server IDE for Simplicite" \
      org.opencontainers.image.vendor="VSCode server / Simplicite Software" \
      org.opencontainers.image.licenses="NONE" \
      org.opencontainers.image.created="$BUILD_DATE"


ARG MAVEN_VERSION=3.8.6
ARG GRADLE_VERSION=7.6
ARG NODE_VERSION=v18.12.1

USER root
RUN groupadd -g 2000 simplicite && \
    useradd -N -g 2000 -u 2000 -d /var/simplicite simplicite && \
    mkdir /var/simplicite/data /var/simplicite/projects /var/simplicite/extensions && \
    chown -R simplicite:simplicite /var/simplicite/data /var/simplicite/projects /var/simplicite/extensions && \
    
    dnf upgrade -y && \
    dnf install -y epel-release && \
    dnf install -y bash git vim-enhanced iputils bind bind-utils nmap openssh-clients openssl curl wget zip unzip xz ca-certificates libjpeg fontconfig && \
    dnf clean all && \
    cd /usr/local && \
    echo "Installing Node.js $NODE_VERSION" && \
    wget -qO- https://nodejs.org/dist/$NODE_VERSION/node-$NODE_VERSION-linux-x64.tar.xz | tar xfJ - && \
    chown -R simplicite:simplicite /usr/local/node-$NODE_VERSION-linux-x64 && \
    
    echo "Installing VSCode server" && \
    curl -fsSL https://code-server.dev/install.sh | sh

ENV LANG=en_US.UTF-8 \

    NODE_HOME=/usr/local/node-$NODE_VERSION-linux-x64 \
    PATH=/bin:/usr/local/node-$NODE_VERSION-linux-x64/bin:$PATH \
    SERVICE_URL=https://open-vsx.org/vscode/gallery \
    ITEM_URL=https://open-vsx.org/vscode/item \
    LOG4J_FORMAT_MSG_NO_LOOKUPS=true

USER simplicite
COPY --chown=simplicite:simplicite config.yaml /var/simplicite/.config/code-server/
# COPY --chown=simplicite:simplicite keybindings.json /var/simplicite/data/User/
COPY --chown=simplicite:simplicite simplicite.code-snippets /var/simplicite/data/User/snippets/

RUN git config --global user.name "Simplicite Software" && \
    git config --global user.email "contact@simplicitesoftware.com" && \
    git config --global http.sslVerify "false" && \

    /usr/bin/code-server --verbose --extensions-dir=/var/simplicite/extensions --install-extension octref.vetur && \
    echo -e "alias vi=vim\n\
alias rm='rm -i'\n\
alias cp='cp -i'\n\
alias mv='mv -i'\n\
alias dir='ls -alF --color=always'" >> /var/simplicite/.bashrc

EXPOSE 3030 3000 8080 8443 8444 8000

WORKDIR /var/simplicite
CMD [ "/usr/bin/code-server", "--user-data-dir=/var/simplicite/data", "--extensions-dir=/var/simplicite/extensions" ]
