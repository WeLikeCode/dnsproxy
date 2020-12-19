# builder image
FROM golang:1.14-alpine3.11 as builder
RUN mkdir /build
ADD ./ /build/
WORKDIR /build
#RUN CGO_ENABLED=0 GOOS=linux go build -a -o golang-memtest .
RUN go build -mod=vendor
#RUN ls -al 

# generate clean, final image for end users
FROM alpine:3.11
RUN mkdir /app
COPY --from=builder /build/dnsproxy /app/dnsproxy

RUN chmod +x /app/dnsproxy
#RUN ls -al /app/

# executable
ENTRYPOINT [ "/app/dnsproxy" ]
# arguments that can be overridden
CMD ["/app/dnsproxy", "-u", "8.8.8.8" ]