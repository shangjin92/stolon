ARG PGVERSION

# base build image
FROM golang:1.16-buster AS build_base

WORKDIR /stolon

# only copy go.mod and go.sum
COPY go.mod .
COPY go.sum .

RUN go mod download

#######
####### Build the stolon binaries
#######
FROM build_base AS builder

# copy all the sources
COPY . .

RUN make

#######
####### Build the final image
#######
FROM postgres:$PGVERSION

RUN useradd -ms /bin/bash stolon

EXPOSE 5432

# copy the agola-web dist
COPY --from=builder /stolon/bin/ /usr/local/bin

# copy zhparser extension
COPY --from=builder /stolon/extensions/zhparser/pgsql-10/tsearch_data/ /usr/share/postgresql/10/tsearch_data
COPY --from=builder /stolon/extensions/zhparser/pgsql-10/usr-local-lib/ /usr/local/lib
COPY --from=builder /stolon/extensions/zhparser/pgsql-10/pg-lib/ /usr/lib/postgresql/10/lib
COPY --from=builder /stolon/extensions/zhparser/pgsql-10/extension/ /usr/share/postgresql/10/extension

RUN chmod +x /usr/local/bin/stolon-keeper /usr/local/bin/stolon-sentinel /usr/local/bin/stolon-proxy /usr/local/bin/stolonctl
