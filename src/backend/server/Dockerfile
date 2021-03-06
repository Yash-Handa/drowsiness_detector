# syntax = docker/dockerfile:1.0-experimental

# All dependencies are set here
FROM golang:1.15.2-buster AS base

LABEL author "Yash Handa"
LABEL description "Backend for the Drowsiness Detection App"

WORKDIR /go/src/server
COPY go.* ./
RUN go mod download -x; go mod tidy -v

# here dev/local stage is set
FROM base AS local
RUN GO111MODULE=off go get -v github.com/rubenv/sql-migrate/... \
  github.com/volatiletech/sqlboiler \
  github.com/99designs/gqlgen
CMD [ "/bin/bash" ]

# A testing stage for the app
FROM base AS test
COPY . .
RUN go test  -v ./...

# here the static app binary (CGO_ENABLED=0) is build
FROM base AS build
ENV CGO_ENABLED=0
COPY . .
RUN go build -o /bin/server ./cmd/server/main.go

# This results in a single layer image
FROM scratch AS prod
COPY --from=build /bin/server /server
ENTRYPOINT ["/server"]