ARG BASE_IMAGE
FROM ${BASE_IMAGE}

ARG SOURCE_CODE=intel/runtimes/ml/ubi9-python-3.11

LABEL name="odh-notebook-intel-runtime-ml-ubi9-python-3.11" \
    summary="Runtime Intel® optimized ML notebook image for ODH notebooks" \
    description="Runtime Intel® optimized ML notebook image with base Python 3.11 builder image based on UBI9 for ODH notebooks" \
    io.k8s.display-name="Runtime Intel® optimized ML notebook image for ODH notebooks" \
    io.k8s.description="Runtime Intel® optimized ML notebook image with base Python 3.11 builder image based on UBI9 for ODH notebooks" \
    authoritative-source-url="https://github.com/opendatahub-io/notebooks" \
    io.openshift.build.commit.ref="main" \
    io.openshift.build.source-location="https://github.com/opendatahub-io/notebooks/tree/main/intel/runtimes/ml/ubi9-python-3.11" \
    io.openshift.build.image="quay.io/opendatahub/workbench-images:intel-runtime-ml-ubi9-python-3.11"

WORKDIR /opt/app-root/bin

# Install Python packages from Pipfile.lock
COPY ${SOURCE_CODE}/Pipfile.lock Pipfile.lock

# Copy Elyra dependencies for air-gapped enviroment
COPY ${SOURCE_CODE}/utils utils

COPY --chown=1001:0 ${SOURCE_CODE}/.patch_sklearn.py /opt/app-root/bin/.patch_sklearn.py
ENV PYTHONSTARTUP="/opt/app-root/bin/.patch_sklearn.py"

#Virtualenv creates a symlink of lib to lib64. That causes issues with importing ITEX since both locations will have ITEX library and it will conflict.
RUN echo "Installing softwares and packages" && \
    micropipenv install && \
    rm -f ./Pipfile.lock && \
    # Fix permissions to support pip in Openshift environments \
    chmod -R g+w /opt/app-root/lib/python3.11/site-packages && \
    fix-permissions /opt/app-root -P

WORKDIR /opt/app-root/src
