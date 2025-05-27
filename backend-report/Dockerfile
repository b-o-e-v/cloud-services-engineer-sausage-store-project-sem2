FROM golang:1.24.3-alpine AS builder

RUN apk add --no-cache git

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-w -s" -o server .

FROM alpine:3.21

RUN apk add --no-cache ca-certificates && update-ca-certificates
RUN addgroup -g 1001 -S srvgrp && adduser -u 1001 -S srvusr -G srvgrp

WORKDIR /app

COPY --from=builder /app/server .

USER srvusr
EXPOSE 8080

HEALTHCHECK \
  --interval=30s \
  --timeout=5s \
  --start-period=10s \
  --retries=3 \
  CMD wget -q --spider http://localhost:8080/api/v1/health || exit 1

CMD ["./server"]
