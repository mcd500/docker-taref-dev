FROM aistcpsec/tee-distro-dev:doxygen-20.04 AS build
# TO trasioteam/taref-dev:doxygen

# Make sure the following command run as build-user
USER build-user

# For fetching ta-ref.git
ARG CI_JOB_TOKEN=
RUN git config --global user.name gitlab-ci-token
RUN git config --global http.sslVerify false
RUN git config --global credential.helper 'store --file ~/.git-credentials'
RUN echo https://gitlab-ci-token:${CI_JOB_TOKEN}@192.168.100.100:443 >> ~/.git-credentials
RUN echo https://gitlab-ci-token:${CI_JOB_TOKEN}@192.168.100.100 >> ~/.git-credentials

# Set TAREF_REF dir
ENV TAREF_DIR=${USER_DIR}/ta-ref

# Export TEE Variable, else makefile would throw error
ENV TEE=keystone
WORKDIR $USER_DIR

# Clone the ta-ref and the given branch
ARG BRANCH=
RUN git clone https://192.168.100.100/rinkai/ta-ref.git && \
    cd ${TAREF_DIR} && \
    git checkout ${BRANCH}

# Set the workdir
WORKDIR $TAREF_DIR

# Make the docs
RUN make docs
