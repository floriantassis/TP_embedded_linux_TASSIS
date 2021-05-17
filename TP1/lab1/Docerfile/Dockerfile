# Please update this skeleton 
# Don't forget to write commentaries in English instead of French for you would be asked to in a work environment!
# Based on Ubuntu "" 20.04 => Your version of Ubuntu or else!

FROM ubuntu:latest as ynov-ctng-ubuntu



# LABEL about the custom image
LABEL maintainer="florian.tassis@ynov.com" 
# Your Ynov Bordeaux Campus student email address
LABEL version="0.1" 
# First version, nothing to change!
LABEL description = "TP docker" 
# Add a relevant description of the image here! (Recommended)

# Make the creation of docker images easier so that CTNG_UID/CTNG_GID have
# a default value if it's not explicitly specified when building. This
# will allow publishing of images on various package repositories (e.g.
# docker hub, gitlab containers). dmgr.sh can still be used to set the
# UID/GID to that of the current user when building a custom container.
ARG CTNG_UID=1000
ARG CTNG_GID=1000
# File to configure for your raspberry pi version
ARG CONFIG_FILE

# Crosstool-ng must be executed from a user that isn't the superuser (root)
# You must create a user and add it to the sudoer group
# Help : https://phoenixnap.com/kb/how-to-create-sudo-user-on-ubuntu
# https://phoenixnap.com/kb/how-to-create-sudo-user-on-ubuntu

RUN adduser --disabled-password --gecos '' florian_tassis
RUN adduser florian_tassis sudo
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# You will need to update the repository list before updating your system in order to install some of the packages
# Use the sources.list provided with the lab materials
# On ubuntu, lookup the command add-apt-repository and the repos universe and multiverse?

RUN apt-get -y update && apt-get -y upgrade 

RUN TZ="Europe/Paris" apt-get install -y tzdata

# Install necessary packages to run crosstool-ng
# You don't remember the previous lectures on the crosstool-ng?
# Use google : install crosstool-ng <Your distribution>??
RUN apt-get install -y gcc g++ bison flex install-info make texinfo libncurses5-dev python3-dev autoconf automake libtool libtool-bin gawk wget bzip2 xz-utils unzip patch libstdc++6 rsync git help2man

# Install Dumb-init
# https://github.com/Yelp/dumb-init
RUN apt install dumb-init && echo 'export PATH=/opt/ctng/bin:$PATH' >> /etc/profile
ENTRYPOINT [ "/usr/local/bin/dumb-init", "--" ]

# Login with user 
USER florian_tassis
WORKDIR /home/florian_tassis
# Download and install the latest version of crosstool-ng
# https://github.com/crosstool-ng/crosstool-ng.git
RUN git clone https://github.com/crosstool-ng/crosstool-ng.git ct-ng
WORKDIR /home/florian_tassis/ct-ng
RUN ./bootstrap && ./configure --enable-local && sudo make && sudo make install
ENV PATH=/home/florian_tassis/.local/bin:$PATH
COPY ${CONFIG_FILE} config

# Build ct-ng
RUN ./ct-ng arm-unknown-linux-gnueabi
RUN ./ct-ng build

ENV TOOLCHAIN_PATH=/home/dev/x-tools/${CONFIG_FILE}
ENV PATH=${TOOLCHAIN_PATH}/bin:$PATH

CMD ["bash"]
