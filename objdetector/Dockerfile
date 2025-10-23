FROM balenalib/raspberrypi4-64-debian:bullseye

ARG MAYOR_REVISION
ARG MINOR_REVISION
ARG BUILD_ID

#labeling
LABEL org.label-schema.name="rPIFocus" \
    org.label-schema.description="Docker running object detector application for the Raspberry Pi HD Camera" \
    org.label-schema.url="https://lemariva.com" \
    org.label-schema.vcs-url="https://github.com/lemariva/rPIFocus" \
    org.label-schema.vendor="LeMaRiva|Tech info@lemariva.com" \
    org.label-schema.version="${MAYOR_REVISION}.${MINOR_REVISION}.${BUILD_ID}"

RUN apt update
RUN apt install apt-utils 
RUN apt full-upgrade
RUN apt install -y --no-install-recommends wget git python3-dev python3-pip python3-setuptools python3-wheel \
    python3-numpy libhdf5-dev libhdf5-serial-dev libhdf5-103 libqt5gui5 libqt5webkit5 libqt5test5 \
    python3-pyqt5 python3-pil build-essential feh pkg-config libjpeg-dev zlib1g-dev libssl-dev libffi-dev \
    libraspberrypi0 libraspberrypi-dev libraspberrypi-bin libfreetype6-dev libxml2  libopenjp2-7 libatlas-base-dev \
    libopenexr-dev libilmbase25 libqt5gui5 libqt5test5 libavformat-dev libswscale-dev libavcodec-dev libv4l-dev libgtk-3-0 unzip

RUN python3 -m pip install --upgrade pip

RUN python3 -m pip install pyzmq==21.0.0

COPY ./app/requirements.txt /tmp/requirements.txt
RUN pip3 install -r /tmp/requirements.txt

RUN python3 -m pip install --extra-index-url https://google-coral.github.io/py-repo/ pycoral

COPY ./edgetpu_runtime /tmp/edgetpu_runtime
RUN cd /tmp/edgetpu_runtime && ./install.sh

RUN mkdir /root/app 
WORKDIR /root/app

COPY ./app /root/app

#copy supervisord files
COPY "./conf/supervisord.conf" /etc/supervisor/conf.d/supervisord.conf
RUN mkdir /var/log/supervisord/

RUN apt autoremove \
    && rm -rf /tmp/* \
    && rm -rf /var/lib/apt/lists/*

#supervisord
CMD ["/usr/local/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

EXPOSE 8010

STOPSIGNAL SIGTERM
