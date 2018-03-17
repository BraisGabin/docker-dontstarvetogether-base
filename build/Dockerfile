FROM dstacademy/steamcmd:0.4 as STEAM

ARG DST_HOME
ENV DST_HOME ${DST_HOME:-"/opt/dst"}
ARG DST_BRANCH
ENV DST_BRANCH ${DST_BRANCH}
ARG DST_BRANCH_PASSWORD
ENV DST_BRANCH_PASSWORD ${DST_BRANCH_PASSWORD}

RUN mkdir -p $DST_HOME && chown $STEAM_USER:$STEAM_USER $DST_HOME
RUN gosu $STEAM_USER steamcmd \
	+@ShutdownOnFailedCommand 1 \
	+login anonymous \
	+force_install_dir $DST_HOME \
	+app_update 343050 \
		$([ -n "$DST_BRANCH" ] && printf %s "-beta $DST_BRANCH") \
		$([ -n "$DST_BRANCH_PASSWORD" ] && printf %s "-betapassword $DST_BRANCH_PASSWORD") \
		validate \
	+quit

FROM scratch
MAINTAINER DST Academy <support@dst.academy>
COPY --from=STEAM $DST_HOME $DST_HOME
