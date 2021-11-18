FROM debian:bullseye

ENV TZ=Europe/Paris
RUN rm -f /etc/localtime && ln -s /usr/share/zoneinfo/Europe/Paris /etc/localtime

# stable backport used to install python2-dev
RUN echo "deb http://deb.debian.org/debian bullseye-backports main" > /etc/apt/sources.list.d/bullseye-backport.list

RUN apt-get update && \
	apt-get install -y \
		git \
		postgresql-server-dev-all \
		libsqlite3-dev \
		python3.9-dev \
		python3-pip \
		python \
		python2-dev \
		2to3 \
		libzmq3-dev \
		debhelper \
		build-essential \
		cmake \
		ninja-build \
		gcc \
		g++ \
		libboost-all-dev \
    liblog4cplus-2.0.5 \
		liblog4cplus-dev \
		libprotobuf-dev \
		protobuf-compiler \
		libproj19 \
		libproj-dev \
		libpqxx-dev \
		rabbitmq-server \
		libgeos-c1v5 \
		libgoogle-perftools4 \
		libgoogle-perftools-dev \
		vim \
    python3-protobuf \
    clang-format \
		ccache \
    libssl-dev && \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# create copy of source list
RUN cp /etc/apt/sources.list /etc/apt/sources.list.tmp
# use unstable repo for osmpbf 1.5.0-2
RUN echo "deb http://http.us.debian.org/debian testing main non-free contrib" > /etc/apt/sources.list

RUN apt-get update && \
		apt-get install -y \
        	libosmpbf-dev \
		&& apt-get clean \
		&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# rollback to default sources.list file
RUN cp /etc/apt/sources.list.tmp /etc/apt/sources.list \
	    && rm /etc/apt/sources.list.tmp

# install pip for python2.7
RUN curl https://bootstrap.pypa.io/pip/2.7/get-pip.py -o get-pip.py && python2 get-pip.py \
     && pip install -U virtualenv pipenv \
		 && rm -rf /tmp/* /var/tmp/* ~/.cache/pip/*


# add user and group jenkins, with specific userid and groupid, never fail
RUN groupadd -g 115 jenkins; exit 0
RUN useradd -u 109 -g 115 jenkins | chpasswd; exit 0

CMD ["/usr/sbin/rabbitmq-server"]
