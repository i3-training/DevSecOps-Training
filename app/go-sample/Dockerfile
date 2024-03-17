#build stage
FROM golang:alpine AS builder
RUN apk add --no-cache git
WORKDIR /go/src/app
COPY . .
RUN go get -d -v ./...
RUN go build -o /go/bin/app -v ./...

#final stage
FROM nginx:1.25.4-alpine3.18
RUN apk --no-cache add ca-certificates
COPY --from=builder /go/bin/app /app
COPY configs/ /
RUN chmod +x /entrypoint.sh
ENTRYPOINT /entrypoint.sh
LABEL Name=go-rest Version=0.0.1
EXPOSE 9090