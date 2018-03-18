FROM dstacademy/steamcmd:0.4 as STEAM
MAINTAINER DST Academy <support@dst.academy>

# Set build arguments.
ENV DST_HOME "/opt/dst"
ARG DST_BRANCH
ENV DST_BRANCH ${DST_BRANCH}
ARG DST_BRANCH_PASSWORD
ENV DST_BRANCH_PASSWORD ${DST_BRANCH_PASSWORD}

# Install Don't Starve Together Server.
RUN set -x \
	&& mkdir -p $DST_HOME \
	&& chown $STEAM_USER:$STEAM_USER $DST_HOME \
	&& sync \
	&& gosu $STEAM_USER steamcmd \
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

ENV DST_USER "dst"
ENV DST_HOME "/opt/dst"

# Add our user/group first to ensure their IDs get set consistently.
RUN groupadd -r $DST_USER && useradd -rm -d $DST_HOME -g $DST_USER $DST_USER

# Install dependencies.
RUN set -x \
	&& dpkg --add-architecture i386 \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends \
		lib32stdc++6 \
		libcurl4-gnutls-dev:i386 \
	&& apt-get clean \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY --from=STEAM --chown=dst:dst $DST_HOME $DST_HOME
