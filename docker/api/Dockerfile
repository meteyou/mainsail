FROM debian:buster AS base

RUN groupadd --force -g 1000 node && \
    useradd -ms /bin/bash --no-user-group -g 1000 -u 1000 node

RUN apt-get update && \
    apt-get install -y --no-install-recommends sudo wget cmake swig git virtualenv python-dev libffi-dev \
        build-essential libncurses-dev libusb-dev avrdude gcc-avr binutils-avr \
        avr-libc dfu-util libnewlib-arm-none-eabi gcc-arm-none-eabi \
        binutils-arm-none-eabi libusb-1.0 && \
    rm -rf /var/lib/apt/lists/*

# Install libsim
RUN cd /tmp && \
    wget http://download.savannah.nongnu.org/releases/simulavr/libsim_1.1.0_amd64.deb && \
    apt-get install ./libsim_1.1.0_amd64.deb && \
    rm libsim_1.1.0_amd64.deb

#prebuild files for simulavr, not needed currently, because we just build the python lib later
#RUN wget http://download.savannah.nongnu.org/releases/simulavr/simulavr_1.1.0_amd64.deb
#RUN apt install /src/simulavr_1.1.0_amd64.deb
#RUN wget http://download.savannah.nongnu.org/releases/simulavr/python3-simulavr_1.1.0_amd64.deb
#RUN apt install /src/python3-simulavr_1.1.0_amd64.deb

RUN cat /etc/passwd

# Allow user node to run sudo without password
RUN echo 'node ALL=(ALL:ALL) NOPASSWD: ALL' > /etc/sudoers.d/node

#if systemd is installed, we need to backup the current systemctl command
#RUN mv /bin/systemctl /bin/systemctl.bak
RUN ln -s /bin/true /bin/systemctl

# Copy entrypoint script
COPY run-api /usr/local/bin/run-api

USER node

WORKDIR /home/node

# Klipper
RUN git clone --depth=1 https://github.com/KevinOConnor/klipper
COPY simulavr.config /home/node/klipper/simulavr.config
COPY linux.config /home/node/klipper/linux.config
RUN cd klipper && \
    mv simulavr.config .config && \
    make && \
    cp out/klipper.elf simulavr.elf && \
    make clean && \
    mv linux.config .config && \
    make && \
    ./scripts/install-debian.sh

# Moonraker
RUN git clone --depth=1 https://github.com/Arksine/moonraker && \
    cd moonraker && \
    sed -E 's/check_klipper\(\)/check_klipper() { return 0; }\nold()/' scripts/install-moonraker.sh > scripts/install-moonraker2.sh && \
    chmod +x scripts/install-moonraker2.sh && \
    ./scripts/install-moonraker2.sh

RUN sudo rm -f /bin/systemctl
#restore backed up systemctl command
#RUN sudo mv /bin/systemctl.bak /bin/systemctl

# SimulAVR
RUN git clone https://git.savannah.nongnu.org/git/simulavr.git && \
    cd simulavr && \
    make python && \
    make build

ENTRYPOINT ["run-api"]
