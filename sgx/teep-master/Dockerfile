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
    git checkout ${BRANCH} && \
    git submodule sync --recursive && \
    git submodule update --init --recursive

# Set the workdir
WORKDIR $TAREF_DIR

# Source the environment variables
#RUN  ["/bin/bash", "-c", "source /opt/intel/sgxsdk/environment"]
#RUN  ["/bin/bash", "-c", "source env/sgx_x64.sh"]
ENV ENABLE_TEE_ENVS=1
ENV TEE=sgx
ENV ARCH=x64
ENV EDGER8R_BIN=${SGX_SDK}/bin/${ARCH}/sgx_edger8r
ENV ENCLAVE_SIGNER_BIN=${SGX_SDK}/bin/${ARCH}/sgx_sign
ENV SGX_LIBRARY_DIR=${SGX_SDK}/lib64
ENV SGX_INCLUDE_DIR=${SGX_SDK}/include
ENV SGX_TRUSTED_INCLUDE_DIR=${SGX_INCLUDE_DIR}/tlibc
ENV SGX_FLAGS="-m64 -Wall -Wextra -Winit-self -Wpointer-arith -Wreturn-type -Waddress -Wsequence-point -Wformat-security -Wmissing-include-dirs -Wfloat-equal -Wundef -Wshadow -Wcast-align -Wcast-qual -Wconversion -Wredundant-decls"
ENV SGX_CFLAGS="${SGX_FLAGS} -Wjump-misses-init -Wstrict-prototypes -Wunsuffixed-float-constants"
ENV SGX_CXXFLAGS="${SGX_FLAGS} -Wnon-virtual-dtor -std=c++11"

ENV CPATH=${SGX_INCLUDE_DIR}

# Build the ta-ref
# RUN make TEST_DIR=test_hello build

# Build the test_hello TA
RUN make build test-bin MACHINE=NUC TEST_DIR=test_hello

# Build the test_gp TA
RUN make build test-bin MACHINE=NUC TEST_DIR=test_gp
