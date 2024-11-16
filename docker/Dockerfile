FROM ubuntu:22.04

ENV DEBIAN_FRONTEND="noninteractive"
ENV LANG=en_NZ.utf8

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
        # Scientific computing dependencies
        libatlas-base-dev \
        gfortran \
        libhdf5-dev \
        libblas-dev \
        liblapack-dev \
        libfreetype6-dev \
    && rm -rf /var/lib/apt/lists/*

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
    # X11 dependencies for FSL
    libx11-6 \
    libxext6 \
    libxt6 \
    libxmu6 \
    libxft2 \
    libxinerama1 \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*


#Install FSl
RUN wget https://fsl.fmrib.ox.ac.uk/fsldownloads/fslconda/releases/fslinstaller.py && \
    python ./fslinstaller.py -d /usr/local/fsl/

#Make FSL Happy
ENV FSLDIR=/usr/local/fsl
ENV PATH=$FSLDIR/bin:$PATH

#Following is the recommended process in upstream repo to source the file. However, this only works during Dockerb build and doesn't persist during runtime
#RUN /bin/bash -c 'source /usr/local/fsl/etc/fslconf/fsl.sh'
#Therefore, using the following methong which will add FSL setup to system-wide profile
RUN echo '. ${FSLDIR}/etc/fslconf/fsl.sh' >> /etc/profile.d/fsl.sh


ENV FSLMULTIFILEQUIT=TRUE
ENV FSLOUTPUTTYPE=NIFTI_GZ

# Install additional packages into FSL's conda environment
RUN . ${FSLDIR}/etc/fslconf/fsl.sh && \
    ${FSLDIR}/bin/conda install -y -c conda-forge \
    jupyterlab \
    numpy \
    scipy \
    pandas \
    matplotlib \
    nilearn \
    mne


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
    && unzip /var/lib/data.zip -d /var/lib \
    && rm -f /var/lib/data.zip \
    && chown -R root:root /var/lib/data \
    && chmod -R o+rX /var/lib/data



# Verify installations: This is more of a sanity check as there were previous tags didn't 
# have these two packages correctly installed yet the container build didn't fail
RUN python3 -c "import nilearn; import mne" || exit 1

