# Stage 1: Build the application
FROM rust:1.81 AS builder

ENV MADARA_VER="v0.8.0"

# Install build dependencies
RUN apt-get -y update && \
    apt-get install -y clang && \
    apt-get autoremove -y; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*
# Set the working directory
WORKDIR /usr/src/madara/

RUN echo Cloning madara from branch ${MADARA_VER}
RUN git clone --branch ${MADARA_VER} --single-branch https://github.com/madara-alliance/madara . && \
    git submodule update --init --recursive

# Build the application in release mode
RUN cargo build --release
# Stage 2: Create the final runtime image
FROM debian:bookworm
# Install runtime dependencies
RUN apt-get -y update && \
    apt-get install -y openssl ca-certificates tini curl &&\
    apt-get autoremove -y; \
    apt-get clean; \
    rm -rf /var/lib/apt/lists/*
# Set the working directory
WORKDIR /usr/local/bin
# Copy the compiled binary from the builder stage
COPY --from=builder /usr/src/madara/target/release/madara .

# Set the entrypoint
ENTRYPOINT ["./madara"]
