# Copyright 2019 Nokia
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

FROM alpine:3.9
MAINTAINER Balazs Szekeres <balazs.szekeres@nokia.com>

ARG KUBEDNS
ENV KUBEDNS_VERSION=$KUBEDNS
ENV GOPATH /build
ENV PATH /usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

RUN apk update \
&&  apk upgrade \
&&  apk add sudo \
&&  apk add --update libcap \
&&  adduser -u 145 -D -H -s /sbin/nologin kubedns \
&&  mkdir -p /home/kubedns \
&&  chown -R kubedns:kubedns /home/kubedns \
&&  chown -R kubedns:kubedns /etc \
&&  apk add --no-cache --virtual .build-deps build-base git mercurial go glide bash tar curl \
&&  apk add --no-cache dnsmasq \
&&  mkdir -p $GOPATH/src/k8s.io/dns \
&&  curl -fsSL -k https://github.com/kubernetes/dns/archive/${KUBEDNS_VERSION}.tar.gz | tar zx --strip-components=1 -C $GOPATH/src/k8s.io/dns \
&&  cd $GOPATH/src/k8s.io/dns \
&&  PKG=k8s.io/dns ARCH=arm64 VERSION=${KUBEDNS_VERSION} ./build/build.sh \
&&  mv $GOPATH/bin/kube-dns /usr/bin/kube-dns \
&&  mv $GOPATH/bin/dnsmasq-nanny /usr/bin/dnsmasq-nanny \
&&  setcap 'cap_net_bind_service=+ep' /usr/sbin/dnsmasq \
&&  apk del .build-deps \
&&  rm -rf $GOPATH \
&&  rm -rf /build \
&&  rm -rf /root/.glide

USER kubedns
ENV HOME /home/kubedns
WORKDIR $HOME


