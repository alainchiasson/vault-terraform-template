FROM quay.io/centos/centos:stream

RUN yum install -y jq openssl yum-utils wget which diffutils unzip git
RUN yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
RUN yum -y install terraform

COPY scripts/ /usr/local/bin
RUN chmod +x /usr/local/bin/*

WORKDIR /workdir

CMD ["/bin/bash", "-c", "exec /bin/bash -c 'trap : TERM INT; sleep 999999999d & wait'"]
