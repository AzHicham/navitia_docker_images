FROM debian:8

# use UTC as localtime
RUN rm -f /etc/localtime && ln -s /usr/share/zoneinfo/Europe/Paris /etc/localtime

RUN apt-get update && \
    apt-get install -y python-pip \
        wget \
        postgresql-server-dev-all \
        libsqlite3-dev \
        python-dev \
        libzmq-dev \
        debhelper \
        build-essential \
        cmake \
        gcc \
        g++ \
        libboost-all-dev \
        liblog4cplus-1.0-4 \
        liblog4cplus-dev && \
    apt-get install -y --force-yes \
        libproj0 \
        libproj-dev \
        libpqxx3-dev \
        rabbitmq-server \
        libgeos-c1 \
        libgoogle-perftools4 \
        libgoogle-perftools-dev \
        curl \
        vim && \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install Python 3.6 needed by black for our pre-commits
RUN wget 'https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tgz' -O - | tar -xz -C /tmp/      \
    && cd /tmp/Python-3.6.8     \
    && ./configure              \
    && make -j$(nproc)          \
    && make altinstall		\
    && rm -rf /tmp/*

# Add address of the canaltp.local apt server
RUN echo "deb http://apt.canaltp.local/debian/repositories jessie-dev main" > /etc/apt/sources.list.d/canaltp.list
RUN echo "nameserver 10.50.83.15" > /etc/resolv.conf \
		&& apt-get update \
		# Get the gpg key to authorize downloads from local apt server
		&& wget --output-document=- http://apt.canaltp.local/debian/repositories/canaltp.gpg.key | apt-key add - \
		&& apt-get -y --force-yes -t jessie-dev install libosmpbf-dev libprotobuf-dev protobuf-compiler python-protobuf \
		&& apt-get clean \
		&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py && python get-pip.py \
        && pip install -U virtualenv pipenv && \
        # install dependancies for libc
        pip install ujson==1.33 numpy==1.9 && \
        rm -rf /tmp/* /var/tmp/* ~/.cache/pip/*

# Install the latest and Greatest of Git !
RUN apt-get update \
    && apt-get install -y dh-autoreconf libcurl4-gnutls-dev libexpat1-dev gettext libz-dev libssl-dev \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
RUN curl -k https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.26.2.tar.xz | tar -xJ -C /tmp/ \
    && cd /tmp/git-2.26.2/ \
    && make configure \
    && ./configure --prefix=/usr \
    && make \
    && make install \
    && rm -rf /tmp/* /var/tmp/* \
    && git --version


# add user and group jenkins, with specific userid and groupid, never fail
RUN groupadd -g 115 jenkins; exit 0
RUN useradd -u 109 -g 115 jenkins | chpasswd; exit 0

CMD ["/usr/sbin/rabbitmq-server"]
