FROM aistcpsec/tee-dev:keystone-1.0.0 AS build
# TO trasioteam/taref-dev:keystone

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
    git checkout ${BRANCH}

# Set the workdir
WORKDIR $TAREF_DIR

# Source the environment variables using /bin/bash
#RUN  ["/bin/bash", "-c", "env/keystone.sh"]
ENV TEE=keystone
ENV ARCH=riscv64
ENV TOOLPREFIX=${ARCH}-unknown-linux-gnu-
ENV PATH=${KEYSTONE_DIR}/qemu/riscv64-softmmu:${PATH}
ENV ENABLE_TEE_ENVS=1
ENV FLATCC_DIR=${KEYEDGE_DIR}/flatcc
ENV KEYEDGE_BIN=${KEYEDGE_DIR}/bin/keyedge
ENV KEYEDGE_INCLUDE_DIR=${KEYEDGE_DIR}/target/include
ENV FLATCC_INCLUDE_DIR=${FLATCC_DIR}/include/flatcc
ENV FLATCC_BIN=${FLATCC_DIR}/bin/flatcc

ENV KEYSTONE_SDK_DIR=${KEYSTONE_DIR}/sdk/build64

ENV CPATH=${KEYSTONE_SDK_DIR}/include/edge:${KEYSTONE_SDK_DIR}/include/host:${KEYSTONE_SDK_DIR}/include:${CPATH}

ENV BUILDROOT_OVERLAY_DIR=${KEYSTONE_DIR}/build/overlay
ENV KEYSTONE_DRIVER_MODULE=${BUILDROOT_OVERLAY_DIR}/root/keystone-driver.ko

# Build the ta-ref
RUN make build

# Build the test_hello TA
RUN make test-bin MACHINE=HIFIVE TEST_DIR=test_hello

# Build the test_gp TA
RUN make test-bin MACHINE=HIFIVE TEST_DIR=test_gp
