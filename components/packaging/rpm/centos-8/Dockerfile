ARG GO_IMAGE
ARG DISTRO=centos
ARG SUITE=8
ARG BUILD_IMAGE=${DISTRO}:${SUITE}

FROM ${GO_IMAGE} AS golang

FROM ${BUILD_IMAGE}
ENV GOPROXY=direct
ENV GO111MODULE=off
ENV GOPATH=/go
ENV PATH $PATH:/usr/local/go/bin:$GOPATH/bin
ENV AUTO_GOPATH 1
ENV DOCKER_BUILDTAGS exclude_graphdriver_btrfs seccomp selinux
ENV RUNC_BUILDTAGS seccomp selinux
ARG DISTRO
ARG SUITE
ENV DISTRO=${DISTRO}
ENV SUITE=${SUITE}

# In aarch64 (arm64) images, the altarch repo is specified as repository, but
# failing, so replace the URL.
RUN if [ -f /etc/yum.repos.d/CentOS-Sources.repo ]; then sed -i 's/altarch/centos/g' /etc/yum.repos.d/CentOS-Sources.repo; fi
RUN yum install -y rpm-build rpmlint yum-utils
RUN yum-config-manager --set-enabled PowerTools
COPY SPECS /root/rpmbuild/SPECS
RUN yum-builddep --define '_without_btrfs 1' -y /root/rpmbuild/SPECS/*.spec
COPY --from=golang /usr/local/go /usr/local/go
WORKDIR /root/rpmbuild
ENTRYPOINT ["/bin/rpmbuild"]
