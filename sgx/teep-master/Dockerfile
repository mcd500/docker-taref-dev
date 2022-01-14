FROM aistcpsec/tee-dev:sgx-2.10 AS build
# TO trasioteam/taref-dev:sgx

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

# Source the environment variables
RUN  ["/bin/bash", "-c", "source /opt/intel/sgxsdk/environment"]
RUN  ["/bin/bash", "-c", "source env/sgx_x64.sh"]

# Build the ta-ref
RUN make build

# Build the test_hello TA
RUN make TEST_DIR=test_hello test-bin run

# Build the test_gp TA
RUN make TEST_DIR=test_gp test-bin run
