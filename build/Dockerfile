FROM dstacademy/steamcmd:0.4 as STEAM
MAINTAINER DST Academy <support@dst.academy>

# Set build arguments.
ENV DST_HOME "/opt/dst"
ARG DST_BRANCH
ENV DST_BRANCH ${DST_BRANCH}
ARG DST_BRANCH_PASSWORD
ENV DST_BRANCH_PASSWORD ${DST_BRANCH_PASSWORD}

# Install Don't Starve Together Server.
RUN set -ex; \
	mkdir -p $DST_HOME; \
	chown $STEAM_USER:$STEAM_USER $DST_HOME; \
	sync; \
	gosu $STEAM_USER steamcmd \
		+@ShutdownOnFailedCommand 1 \
		+login anonymous \
		+force_install_dir $DST_HOME \
		+app_update 343050 \
			$([ -n "$DST_BRANCH" ] && printf %s "-beta $DST_BRANCH") \
			$([ -n "$DST_BRANCH_PASSWORD" ] && printf %s "-betapassword $DST_BRANCH_PASSWORD") \
			validate \
		+quit

FROM debian:9-slim
MAINTAINER DST Academy <support@dst.academy>

ENV GOSU_VERSION 1.10
ENV DST_USER "dst"
ENV DST_HOME "/opt/dst"

# Add our user/group first to ensure their IDs get set consistently.
RUN groupadd -r $DST_USER && useradd -rm -d $DST_HOME -g $DST_USER $DST_USER

# Install dependencies.
RUN set -ex; \
	\
	fetchGosuDeps=' \
		ca-certificates \
		dirmngr \
		gnupg \
		wget \
	'; \
	dpkg --add-architecture i386; \
	apt-get update; \
	apt-get install -y --no-install-recommends \
		$fetchGosuDeps \
		lib32stdc++6 \
		libcurl4-gnutls-dev:i386; \
	rm -rf /var/lib/apt/lists/*; \
	\
	dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')"; \
	wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch"; \
	wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc"; \
	\
# verify the signature
	export GNUPGHOME="$(mktemp -d)"; \
	gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4; \
	gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu; \
	rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc; \
	\
	chmod +x /usr/local/bin/gosu; \
# verify that the binary works
	gosu nobody true; \
	\
	apt-get purge -y --auto-remove $fetchGosuDeps

COPY --from=STEAM --chown=dst:dst $DST_HOME $DST_HOME

# Copy common scripts.
COPY /script/* /usr/local/bin/

CMD dontstarve_dedicated_server_nullrenderer
