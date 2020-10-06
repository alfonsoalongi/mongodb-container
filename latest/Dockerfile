FROM centos/s2i-core-centos7

ENV SUMMARY="MongoDB NoSQL database server" \
    DESCRIPTION="MongoDB (from humongous) is a free and open-source \
cross-platform document-oriented database program. Classified as a NoSQL \
database program, MongoDB uses JSON-like documents with schemas. This \
container image contains programs to run mongod server."

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.display-name="MongoDB 4.4.1" \
      io.openshift.expose-services="27017:mongodb" \
      io.openshift.tags="database,mongodb,rh-mongodb-4.4.1" \
      com.redhat.component="rh-mongodb-4.4.1-container" \
      name="centos/mongodb--4.4.1" \
      usage="docker run -d -e MONGODB_ADMIN_PASSWORD=my_pass centos/mongodb--4.4.1" \
      version="1" \
      maintainer="SoftwareCollections.org <sclorg@redhat.com>"

ENV MONGODB_VERSION=4.4.1 \
    # Set paths to avoid hard-coding them in scripts.
    APP_DATA=/opt/app-root/src \
    HOME=/var/lib/mongodb \
    CONTAINER_SCRIPTS_PATH=/usr/share/container-scripts/mongodb \
    # Incantations to enable Software Collections on `bash` and `sh -i`.
    ENABLED_COLLECTIONS=rh-mongodb-4.4.1 \
    BASH_ENV="\${CONTAINER_SCRIPTS_PATH}/scl_enable" \
    ENV="\${CONTAINER_SCRIPTS_PATH}/scl_enable" \
    PROMPT_COMMAND=". \${CONTAINER_SCRIPTS_PATH}/scl_enable"

EXPOSE 27017

ENTRYPOINT ["container-entrypoint"]
CMD ["run-mongod"]

COPY mongodb-org-4.4.repo /etc/yum.repos.d/mongodb-org-4.4.repo
RUN yum install -y centos-release-scl && \
    INSTALL_PKGS="bind-utils gettext iproute rsync tar mongodb-org-4.4.1 mongodb-org-server-4.4.1 mongodb-org-shell-4.4.1 mongodb-org-mongos-4.4.1 mongodb-org-tools-4.4.1 groff-base" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum -y clean all --enablerepo='*'

COPY s2i/bin/ $STI_SCRIPTS_PATH

COPY root /

# Container setup
RUN : > /etc/mongod.conf && \
    mkdir -p ${HOME}/data && \
    # Set owner 'mongod:0' and 'g+rw(x)' permission - to avoid problems running container with arbitrary UID
    /usr/libexec/fix-permissions /etc/mongod.conf ${CONTAINER_SCRIPTS_PATH}/mongod.conf.template \
    ${HOME} ${APP_DATA}/.. && \
    usermod -a -G root mongod && \
    rpm-file-permissions

VOLUME ["/var/lib/mongodb/data"]

USER 184
