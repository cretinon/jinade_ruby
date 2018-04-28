ARG ARCH=frommakefile
ARG DISTRIB=frommakefile
ARG DOCKERSRC=frommakefile
ARG USERNAME=frommakefile
#
FROM ${USERNAME}/${DOCKERSRC}:${DISTRIB}_${ARCH}
#
ENV RUBY_MAJOR 2.4
ENV RUBY_VERSION 2.4.4
ENV RUBY_DOWNLOAD_SHA256 254f1c1a79e4cc814d1e7320bc5bdd995dc57e08727d30a767664619a9c8ae5a
#
RUN apk add --no-cache --purge --virtual .build-deps -uU openssl-dev libffi-dev zlib-dev libc-dev libffi-dev linux-headers build-base \
    && apk add --no-cache --purge -uU curl git openssl libffi ca-certificates tzdata autoconf \
#    && apk del --purge .build-deps \
    && rm -rf /var/cache/apk/* /tmp/*
    
RUN mkdir -p /usr/src/ruby \
    && curl -fSL -o ruby.tar.gz "http://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.gz" \
    && echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.gz" | sha256sum -c - \
    && tar -xzf ruby.tar.gz -C /usr/src/ruby --strip-components=1 \
    && rm ruby.tar.gz \
    && cd /usr/src/ruby \
    && autoconf \
    && ./configure --disable-install-doc --disable-install-rdoc --disable-install-capi \
    && make -j"$(nproc)" \
    && make install \
    && cd /tmp && rm -rf /usr/src/ruby

RUN echo 'gem: --no-document' > /etc/gemrc \
    && echo 'install: --no-document' >> /etc/gemrc \
    && echo 'update: --no-document' >> /etc/gemrc \
    && gem install bundler json rake \
    && gem cleanup \
    && rm -rf /usr/lib/ruby/gems/*/cache/*															