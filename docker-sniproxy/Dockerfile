FROM alpine:latest

MAINTAINER derd3000@gmail.com

RUN apk add --no-cache\
      sniproxy curl vim wget bash iputils bind-tools\
      iproute2 util-linux net-tools tcpdump mtr iftop\
      iperf iptables psmisc socat jq lsof ca-certificates\
      findutils sipcalc grep openntpd ip6tables openssl\
      procps gawk coreutils libev libressl autoconf\
      automake bsd-compat-headers gettext-dev\
      git sed libev-dev libtool pcre-dev gcc cmake make \
      pkgconfig g++ libtool binutils nano ccache ccache-doc cmake-doc	 

WORKDIR /root

## clone and build udns from source
RUN git clone https://github.com/ortclib/udns.git
WORKDIR /root/udns

## Change default nameserver port which will be used by sniporxy to 5399
RUN sed -i '0,/53/{s/53/5399/}' /root/udns/udns.h

RUN ./configure && \
	make sharedlib && \
	make

RUN install -D -m0755 dnsget "/usr/bin/dnsget"
RUN install -D -m0755 rblcheck "/usr/bin/dnsget"
RUN install -D -m0755 ex-rdns "/usr/bin/dnsget"

RUN install -D -m0644 udns.h "/usr/include/udns.h"
RUN install -D -m0755 libudns.so.0 "/usr/lib/libudns.so.0"
RUN ln -s libudns.so.0 "/usr/lib/libudns.so"

RUN install -D -m0644 dnsget.1 "/usr/share/man/man1/dnsget.1"
RUN install -D -m0644 rblcheck.1 "/usr/share/man/man1/rblcheck.1"
RUN install -D -m0644 udns.3 "/usr/share/man/man3/udns.3"

WORKDIR /root

ADD functions ./

## build sniproxy from source to respect the new nameserver port

RUN git clone https://github.com/dlundquist/sniproxy.git

WORKDIR /root/sniproxy
RUN ./autogen.sh  
RUN ./configure \
	--prefix=/usr \
	--sysconfdir=/etc \
	--mandir=/usr/share/man \
	--infodir=/usr/share/info
RUN make
RUN make install

WORKDIR /root

ADD run.sh ./

CMD ./run.sh
