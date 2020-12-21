# builder image
FROM golang:1.14-alpine3.11 as builder
RUN mkdir /build
ADD ./ /build/
WORKDIR /build
#RUN CGO_ENABLED=0 GOOS=linux go build -a -o golang-memtest .
RUN go build -mod=vendor
#RUN ls -al 

FROM pgarrett/openssl-alpine as certgen
ENV COUNTRY ME
#RUN openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout server.key -out server.crt
#WORKDIR /
ENV CERT_DIR /etc/ssl/certs/
ENV ROOT_NAME quic-server
ENV ROOT_CN *.quic-server
ENV DAYS 3650
ENV RSA_KEY_NUMBITS 4096
#RUN sh -c 'echo $CERT_DIR'
RUN /docker-entrypoint.sh && find / -name *.crt && cp -r /etc/ssl/certs/ /certs ;
#RUN ls -al /etc/ssl/certs/ && find / -name *.crt
#RUN cat /docker-entrypoint.sh


# generate clean, final image for end users
FROM alpine:3.11
RUN mkdir /app
WORKDIR /app
COPY --from=builder /build/dnsproxy /app/dnsproxy
COPY --from=certgen /certs/quic-server.crt /app/example.crt
COPY --from=certgen /certs/quic-server.key /app/example.key

RUN apk update && apk add ca-certificates && rm -rf /var/cache/apk/*
RUN cp /app/example.crt /usr/local/share/ca-certificates/mycert.crt

RUN update-ca-certificates


RUN chmod +x /app/dnsproxy
#RUN ls -al /app/

# executable
ENTRYPOINT [ "/app/dnsproxy" ]
# arguments that can be overridden
CMD ["/app/dnsproxy", "-u", "8.8.8.8" ]