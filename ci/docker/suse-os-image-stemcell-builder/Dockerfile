FROM opensuse:42.3

ENV GOROOT /usr/local/go
ENV PATH $GOROOT/bin:$PATH
RUN echo 'export PATH=$GOROOT/bin:$PATH' >> /etc/profile.d/go.sh

RUN zypper -n in libmysqld-devel sqlite3-devel postgresql-devel libxslt-devel libxml2-devel libxml2 python3-kiwi wget sudo gcc-c++ curl git kpartx dracut libopenssl-devel which make patch

RUN zypper ar https://download.opensuse.org/repositories/devel:/languages:/ruby/openSUSE_Leap_42.3/ ruby
RUN zypper --gpg-auto-import-keys -n in ruby2.3-devel

RUN ln -sf /usr/bin/ruby.ruby2.3 /usr/bin/ruby && ln -sf /usr/bin/gem.ruby2.3 /usr/bin/gem && ln -sf /usr/bin/rake.ruby2.3 /usr/bin/rake

ENV OVF_TOOL_INSTALLER VMware-ovftool-4.1.0-2459827-lin.x86_64.bundle
ENV OVF_TOOL_INSTALLER_SHA1 b907275c8d744bb54717d24bb8d414b19684fed4
ADD ${OVF_TOOL_INSTALLER} /tmp/ovftool_installer.bundle
ADD scripts/install-ovf.sh /tmp/install-ovf.sh
RUN /tmp/install-ovf.sh && rm /tmp/install-ovf.sh

RUN gem install bundler '--version=1.11.2' --no-format-executable

ADD scripts/install-go.sh /tmp/install-go.sh
RUN /tmp/install-go.sh && rm /tmp/install-go.sh
RUN ln -s /usr/local/go/bin/go /usr/bin

RUN gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4
RUN curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/1.9/gosu-amd64" \
    && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/1.9/gosu-amd64.asc" \
    && gpg --verify /usr/local/bin/gosu.asc \
    && rm /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu

RUN useradd --shell /bin/bash -u 1000 -o -c "" -m user \
    && echo 'user ALL=NOPASSWD:ALL' >> /etc/sudoers \
    && usermod -G users user \
    && mkdir /mnt/stemcells \
    && chown user /mnt/stemcells

USER user:users

CMD ["/bin/bash", "-l"]
