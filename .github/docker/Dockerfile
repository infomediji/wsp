FROM golang:1.23-alpine AS builder
WORKDIR /src

RUN apk add --no-cache git ca-certificates && update-ca-certificates
COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -trimpath -ldflags="-s -w" -o /out/wsp_server ./cmd/wsp_server
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -trimpath -ldflags="-s -w" -o /out/wsp_client ./cmd/wsp_client

# ----- Server image -----
FROM alpine:3.20 AS server
RUN apk add --no-cache ca-certificates && adduser -D -u 10001 app
USER 10001
WORKDIR /app
COPY --from=builder /out/wsp_server /usr/local/bin/wsp_server
EXPOSE 8080
ENTRYPOINT ["/usr/local/bin/wsp_server"]

# ----- Client image -----
FROM alpine:3.20 AS client
RUN apk add --no-cache ca-certificates && adduser -D -u 10001 app
USER 10001
WORKDIR /app
COPY --from=builder /out/wsp_client /usr/local/bin/wsp_client
ENTRYPOINT ["/usr/local/bin/wsp_client"]
