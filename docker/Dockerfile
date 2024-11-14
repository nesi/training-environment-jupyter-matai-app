FROM ubuntu:22.04

ENV FSLDIR          "/usr/local/fsl"
ENV DEBIAN_FRONTEND "noninteractive"
ENV LANG en_NZ.utf8

# Forked from ML102
# - replace urls and difrectories
# - replace commit hash

# install system dependencies
# TODO: move ldap-utils, libnss-ldapd, libpam-ldapd, nscd, nslcd to base image??
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        curl \
        git \
        ldap-utils \
        libnss-ldapd \
        libpam-ldapd \
        less \
        nano \
        nodejs \
        nscd \
        nslcd \
        python-is-python3 \
        python3 \
        python3-pip \
        rsync \
        unzip \
        vim \
        wget \
        zip \
        file \
        dc \
        mesa-utils \
        pulseaudio \
        libquadmath0 \
        libgtk2.0-0 \
        firefox \
        libgomp1 \
    && rm -rf /var/lib/apt/lists/*


RUN wget https://fsl.fmrib.ox.ac.uk/fsldownloads/fslconda/releases/fslinstaller.py && \
    python ./fslinstaller.py -d /usr/local/fsl/

ENTRYPOINT [ "sh", "-c", ". /usr/local/fsl/etc/fslconf/fsl.sh && /bin/bash" ]

# install jupyterlab
RUN pip3 --no-cache-dir install jupyterlab
RUN pip3 --no-cache-dir install numpy
RUN pip3 --no-cache-dir install scipy
RUN pip3 --no-cache-dir install pandas
RUN pip3 --no-cache-dir install matplotlib
RUN pip3 --no-cache-dir install nilearn
RUN pip3 --no-cache-dir install mne



## install mrtrix3 requirements
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \ 
    libeigen3-dev \
    zlib1g-dev \
    libqt5opengl5-dev \
    libqt5svg5-dev \
    libgl1-mesa-dev \
    libfftw3-dev \
    libtiff5-dev \
    libpng-dev \
    libssl-dev \
    cmake \
    ninja-build \
    && rm -rf /var/lib/apt/lists/*

# Clone MRtrix3 repository
WORKDIR /opt
RUN git clone https://github.com/MRtrix3/mrtrix3.git

# Configure and build MRtrix3 without GUI
WORKDIR /opt/mrtrix3
RUN ./configure -nogui && \
    ./build

# Add MRtrix3 to PATH
ENV PATH="/opt/mrtrix3/bin:${PATH}"


RUN wget -nv https://github.com/nesi/training-environment-jupyter-matai-app/releases/download/v0.1.0/data.zip \
        -O /var/lib/data.zip \
    && unzip /var/lib/data.zip -C /var/lib \
    && rm -f /var/lib/data.zip \
    && chown -R root:root /var/lib/data \
    && chmod -R o+rX /var/lib/data
