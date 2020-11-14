FROM multiarch/qemu-user-static:x86_64-aarch64 as qemu

FROM arm64v8/python:3.7.9-slim-buster AS builder
COPY --from=qemu /usr/bin/qemu-aarch64-static /usr/bin
ENV REPO_ROOT=/grpc
# REPO_ROOT can be any directory of your choice
RUN apt-get update && apt-get install -y git build-essential && \
    git clone -b v1.33.x https://github.com/grpc/grpc $REPO_ROOT && \
    cd $REPO_ROOT && \
    git submodule update --init && \
    pip install -r requirements.txt && \
    GRPC_PYTHON_BUILD_WITH_CYTHON=1 python setup.py bdist_wheel

FROM arm64v8/python:3.7.9-slim-buster
COPY --from=qemu /usr/bin/qemu-aarch64-static /usr/bin
COPY --from=builder /grpc/dist /grpcio-install
RUN pip install /grpcio-install/*
CMD [ "python" ]