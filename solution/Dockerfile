FROM golang:1.16-alpine AS build-stage

RUN apk add --no-cache git

WORKDIR /app/go-app

COPY go/go.mod .
COPY go/go.sum .

RUN go mod download
COPY go/main.go .

RUN CGO_ENABLED=0 GOOS=linux go build -o /app/out/go-app

FROM gcr.io/distroless/base-debian11 AS build-release-stage

COPY --from=build-stage /app/out/go-app /app/out/go-app
WORKDIR /app/out/
CMD ["./go-app"]
