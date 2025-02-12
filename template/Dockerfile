FROM __DOCKER_ARCH__/debian:buster-20210511-slim

RUN \
    apt-get update \
    # Use pinned versions so that we get updates with build caching
    && apt-get install -y --no-install-recommends \
        python3=3.7.3-1 \
        python3-pip=18.1-5 \
        python3-setuptools=40.8.0-1 \
        python3-pil=5.4.1-2+deb10u2 \
        python3-cryptography=2.6.1-3+deb10u2 \
        iputils-ping=3:20180629-2+deb10u2 \
        git=1:2.20.1-2+deb10u3 \
        curl=7.64.0-4+deb10u2 \
    && rm -rf \
        /tmp/* \
        /var/{cache,log}/* \
        /var/lib/apt/lists/*

# Fix click python3 lang warning https://click.palletsprojects.com/en/7.x/python3/
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

COPY platformio.ini /opt/pio/

RUN \
    # Ubuntu python3-pip is missing wheel
    pip3 install --no-cache-dir \
        wheel==0.36.2 \
        platformio==5.1.1 \
    # Change some platformio settings
    && platformio settings set enable_telemetry No \
    && platformio settings set check_libraries_interval 1000000 \
    && platformio settings set check_platformio_interval 1000000 \
    && platformio settings set check_platforms_interval 1000000 \
    # Build an empty platformio project to force platformio to install all fw build dependencies
    # The return-code will be non-zero since there's nothing to build.
    # Use upload target so that packages required for uploading also get installed
    && (platformio run -d /opt/pio -t upload; echo "Done") \
    && rm -rf /opt/pio/

VOLUME /config
WORKDIR /usr/src/app
