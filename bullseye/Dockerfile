###########################################################
# Dockerfile that builds a Insurgency2 Gameserver
###########################################################
FROM cm2network/steamcmd:root AS build_stage

LABEL maintainer="rrrfffrrr@naver.com"

ENV STEAMAPPID 237410
ENV STEAMAPP insurgency
ENV STEAMAPPDIR "${HOMEDIR}/${STEAMAPP}-dedicated"
ENV DLURL https://raw.githubusercontent.com/rrrfffrrr/Insurgency2

RUN set -x \
	# Add i386 architecture
	&& dpkg --add-architecture i386 \
	# Install, update & upgrade packages
	&& apt-get update \
	&& apt-get install -y --no-install-recommends --no-install-suggests \
		wget=1.21-1+deb11u1 \
		ca-certificates=20210119 \
		lib32z1=1:1.2.11.dfsg-2+deb11u2 \
		libncurses5:i386=6.2+20201114-2+deb11u2 \
		libbz2-1.0:i386=1.0.8-4 \
		lib32ncurses5 \
		lib32bz2-1.0 \
		libtinfo5:i386=6.2+20201114-2+deb11u2 \
		libcurl3-gnutls:i386=7.74.0-1.3+deb11u9 \
	&& mkdir -p "${STEAMAPPDIR}" \
	# Add entry script
	&& wget "${DLURL}/master/etc/entry.sh" -O "${HOMEDIR}/entry.sh" \
	# Create autoupdate config
	&& { \
		echo '@ShutdownOnFailedCommand 1'; \
		echo '@NoPromptForPassword 1'; \
		echo 'force_install_dir '"${STEAMAPPDIR}"''; \
		echo 'login anonymous'; \
		echo 'app_update '"${STEAMAPPID}"''; \
		echo 'quit'; \
	   } > "${HOMEDIR}/${STEAMAPP}_update.txt" \
	&& chmod +x "${HOMEDIR}/entry.sh" \
	&& chown -R "${USER}:${USER}" "${HOMEDIR}/entry.sh" "${STEAMAPPDIR}" "${HOMEDIR}/${STEAMAPP}_update.txt" \
	# Clean up
	&& rm -rf /var/lib/apt/lists/*

FROM build_stage AS bullseye-base

ENV SRCDS_FPSMAX=64 \
	SRCDS_TICKRATE=64 \
	SRCDS_PORT=27015 \
	SRCDS_NET_PUBLIC_ADDRESS="0" \
	SRCDS_IP="0" \
	SRCDS_MAXPLAYERS=16 \
	SRCDS_TOKEN=0 \
	SRCDS_RCONPW="changeme" \
	SRCDS_PW="changeme" \
	SRCDS_STARTMAP="panj push" \
	SRCDS_REGION=255 \
	SRCDS_HOSTNAME="New \"${STEAMAPP}\" Server" \
	SRCDS_WORKSHOP_START_MAP=0 \
	SRCDS_HOST_WORKSHOP_COLLECTION=0 \
	SRCDS_CFG="server.cfg" \
	SRCDS_MAPCYCLE="mapcycle.txt" \
	SRCDS_SECURED=1 \
	SRCDS_LAN=1 \
	SRCDS_WORKSHOP=0

# Switch to user
USER ${USER}

WORKDIR ${HOMEDIR}

CMD ["bash", "entry.sh"]

# Expose ports
EXPOSE 27015/tcp \
	27015/udp

FROM bullseye-base AS bullseye-metamod

ENV METAMOD_VERSION 1.11

FROM bullseye-metamod AS bullseye-sourcemod

ENV SOURCEMOD_VERSION 1.11
