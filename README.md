# Build stage
FROM rust:1-alpine AS builder
RUN apk add --no-cache build-base
WORKDIR /build
COPY . .
RUN cargo build --release

# Runtime stage
FROM alpine:latest
RUN apk add --no-cache ca-certificates && \
    adduser -D -H app
WORKDIR /app
COPY --from=builder /build/target/release/delta-bypass /app/
RUN chown -R app:app /app
USER app
EXPOSE 2233
CMD ["/app/delta-bypass", "--serve", "--host", "0.0.0.0", "--port", "2233"]
