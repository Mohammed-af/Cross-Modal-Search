FROM rust:1.79-bookworm AS builder
RUN apt-get update && apt-get install -y cmake libssl-dev && rm -rf /var/lib/apt/lists/*
WORKDIR /app
COPY Cargo.toml ./
COPY src ./src
RUN cargo build --release

FROM debian:bookworm-slim
RUN apt-get update && apt-get install -y \
    ca-certificates libssl3 postgresql-client wget \
    python3 python3-pip \
    && rm -rf /var/lib/apt/lists/*

RUN pip3 install --break-system-packages pymilvus==2.3.3

RUN wget -q https://dl.min.io/client/mc/release/linux-amd64/mc \
    && chmod +x mc && mv mc /usr/local/bin/

WORKDIR /app
COPY --from=builder /app/target/release/rust-backend /app/rust-backend
COPY insert_to_milvus.py /app/insert_to_milvus.py
RUN chmod +x /app/insert_to_milvus.py

EXPOSE 3000
CMD ["./rust-backend"]
