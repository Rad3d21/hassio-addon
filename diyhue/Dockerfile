ARG BUILD_FROM=hassioaddons/base-python:5.3.4
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Set the build architecture and convert it from hassio to diyhue style
# armhf, armv7, aarch64, amd64, i386
ARG BUILD_ARCH=aarch64
ENV BUILD_ARCHI = ${BUILD_ARCH}

# Other settings
ENV LANG C.UTF-8
ENV DIYHUE_VERSION=master
ENV WORKING_DIR=/opt/hue-emulator

# Create the needed folders
RUN mkdir diyhue config ${WORKING_DIR}

# Install all needed dependencies
RUN apk add -q -u python3 openssl nmap psmisc iproute2 alpine-sdk build-base
RUN apk add --no-cache bluez

# Download diyhue and untar it
RUN curl -J -L -o $diyhue.tar.gz "https://github.com/Rad3d21/diyHue/archive/refs/tags/newtag3.tar.gz" \
	&& tar xzvf $diyhue.tar.gz --strip-components=1 -C diyhue \
	&& rm -rf $diyhue.tar.gz \
	&& pip3 install -r diyhue/requirements.txt --no-cache-dir

# Set working directory to the Archive folder
WORKDIR /opt/hue-emulator

# Install diyHue
RUN mv /diyhue/BridgeEmulator/HueObjects/										./HueObjects/
RUN mv /diyhue/BridgeEmulator/configManager/ 									./configManager/
RUN mv /diyhue/BridgeEmulator/flaskUI/ 											./flaskUI/
RUN mv /diyhue/BridgeEmulator/functions/ 										./functions/
RUN mv /diyhue/BridgeEmulator/lights/ 											./lights/
RUN mv /diyhue/BridgeEmulator/logManager/ 										./logManager/
RUN mv /diyhue/BridgeEmulator/sensors/ 											./sensors/
RUN mv /diyhue/BridgeEmulator/services/ 										./services/
RUN mv /diyhue/BridgeEmulator/HueEmulator3.py 									./
RUN mv /diyhue/BridgeEmulator/genCert.sh /diyhue/BridgeEmulator/openssl.conf 	./

# Copy additional local build files
COPY rootfs ./

# Modify user rights of additional files
RUN chmod +x ./select.sh ./genCert.sh && chmod a+x ./run.sh

# Sleect the right binaries depending on the arch
RUN ./select.sh

# Build arguments
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION

## Document volume
VOLUME ["/config"]

# Labels
LABEL \
	io.hass.name="diyHue" \
	io.hass.description="Fully configurable diyHue Emulator" \
	io.hass.arch="${BUILD_ARCH}" \
	io.hass.type="addon" \
	io.hass.version=${BUILD_VERSION} \
	maintainer="diyHue <info@diyHue.org>" \
	org.opencontainers.image.title="diyHue" \
	org.opencontainers.image.description="Fully configurable diyHue Emulator" \
	org.opencontainers.image.vendor="diyHue" \
	org.opencontainers.image.authors="diyHue <info@diyHue.org>" \
	org.opencontainers.image.licenses="MIT" \
	org.opencontainers.image.url="diyHue.org" \
	org.opencontainers.image.source="" \
	org.opencontainers.image.documentation="" \
	org.opencontainers.image.created=${BUILD_DATE} \
	org.opencontainers.image.revision=${BUILD_REF} \
	org.opencontainers.image.version=${BUILD_VERSION}

CMD [ "./run.sh" ]
