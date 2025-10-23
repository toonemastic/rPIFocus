FROM balenalib/raspberrypi4-64-debian:bullseye

ARG MAYOR_REVISION
ARG MINOR_REVISION
ARG BUILD_ID

#labeling
LABEL org.label-schema.name="rPIFocus" \
    org.label-schema.description="Docker running photo service application (hdr, etc) to take photos using the Raspberry Pi HD Camera" \
    org.label-schema.url="https://lemariva.com" \
    org.label-schema.vcs-url="https://github.com/lemariva/rPIFocus" \
    org.label-schema.vendor="LeMaRiva|Tech info@lemariva.com" \
    org.label-schema.version="${MAYOR_REVISION}.${MINOR_REVISION}.${BUILD_ID}"

RUN apt update
RUN apt install apt-utils 
RUN apt full-upgrade
RUN apt install -y python3-dev python3-pip python3-setuptools python3-wheel python3-numpy \
    libhdf5-dev libhdf5-serial-dev libhdf5-103 libqt5gui5 libqt5webkit5 libqt5test5 python3-pyqt5 \
    libatlas-base-dev libilmbase25 libopenexr-dev libraspberrypi-dev libraspberrypi-bin \
    libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libgtk-3-0

RUN python3 -m pip install --upgrade pip

RUN python3 -m pip install pyzmq==21.0.0

COPY app/requirements.txt /tmp/requirements.txt
RUN pip3 install -r /tmp/requirements.txt

RUN mkdir /root/app
WORKDIR /root/app

COPY ./app/restapi.py /root/app/restapi.py

#copy supervisord files
COPY "./conf/supervisord.conf" /etc/supervisor/conf.d/supervisord.conf
RUN mkdir /var/log/supervisord/

RUN apt autoremove \
    && rm -rf /tmp/* \
    && rm -rf /var/lib/apt/lists/*

#supervisord
CMD ["/usr/local/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

EXPOSE 8005

STOPSIGNAL SIGTERM
