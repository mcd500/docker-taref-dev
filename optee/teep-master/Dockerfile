FROM aistcpsec/tee-dev:optee-3.10.0 AS build
# TO trasioteam/taref-dev:optee

# Make sure the following command run as build-user
USER build-user

# For fetching ta-ref.git
ARG CI_JOB_TOKEN=
RUN git config --global user.name gitlab-ci-token
RUN git config --global http.sslVerify false
RUN git config --global credential.helper 'store --file ~/.git-credentials'
RUN echo https://gitlab-ci-token:${CI_JOB_TOKEN}@192.168.100.100:443 >> ~/.git-credentials
RUN echo https://gitlab-ci-token:${CI_JOB_TOKEN}@192.168.100.100 >> ~/.git-credentials

# Set TAREF_DIR dir
ENV TAREF_DIR=${USER_DIR}/ta-ref

# Set the work directory
WORKDIR $USER_DIR

# Clone the ta-ref and the given branch
ARG BRANCH=
RUN git clone https://192.168.100.100/rinkai/ta-ref.git && \
    cd ${TAREF_DIR} && \
    git checkout ${BRANCH} && \
    git submodule sync --recursive && \
    git submodule update --init --recursive

# Set the workdir
WORKDIR $TAREF_DIR

# Source the environment variables
ENV TEE=optee
ENV MACHINE=SIM
ENV ENABLE_TEE_ENVS=1
ENV OPTEE_OUTBR_DIR=${OPTEE_DIR}/out-br
ENV TOOLPREFIX=${TOOLCHAIN_DIR}/aarch64/bin/aarch64-linux-gnu-
ENV TOOLPREFIX32=${TOOLCHAIN_DIR}/aarch32/bin/arm-linux-gnueabihf-
ENV TA_DEV_DIR=${OPTEE_DIR}/optee_os/out/arm/export-ta_arm64

# Build the ta-ref and build the test_hello TA
RUN make build test-bin MACHINE=RPI3 TEST_DIR=test_hello

# Build the ta-ref and build the test_gp TA
RUN make build test-bin MACHINE=RPI3 TEST_DIR=test_gp