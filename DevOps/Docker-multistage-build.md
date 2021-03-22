# Docker multistage build

Code: Yes
Created: Mar 6, 2021 5:43 PM
Materials: https://docs.docker.com/develop/develop-images/multistage-build/
Reviewed: No
context: docker

# Multi stage build

used mainly in production 

With multi-stage builds, you use multiple `FROM` statements in your Dockerfile. Each `FROM` instruction can use a different base, and each of them begins a new stage of the build. You can selectively copy artifacts from one stage to another, leaving behind everything you don’t want in the final image. To show how this works, let’s adapt the Dockerfile from the previous section to use multi-stage builds

```docker
FROM golang:1.7.3
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=0 /go/src/github.com/alexellis/href-counter/app .
CMD ["./app"]
```

```docker
$ docker build -t alexellis2/href-counter:latest .
```

How does it work? The second `FROM` instruction starts a new build stage with the alpine:latest image as its base. The `COPY --from=0` line copies just the built artifact from the previous stage into this new stage. The Go SDK and any intermediate artifacts are left behind, and not saved in the final image

## Name your  build stage

By default, the stages are not named, and you refer to them by their integer number, starting with 0 for the first `FROM` instruction. However, you can name your stages, by adding an `AS <NAME>` to the `FROM` instruction. This example improves the previous one by naming the stages and using the name in the `COPY` instruction. This means that even if the instructions in your Dockerfile are re-ordered later, the `COPY` doesn’t break.

```docker
FROM golang:1.7.3 AS builder     # the naming here is the improvement
WORKDIR /go/src/github.com/alexellis/href-counter/
RUN go get -d -v golang.org/x/net/html  
COPY app.go    .
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o app .

FROM alpine:latest  
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /go/src/github.com/alexellis/href-counter/app .    # copying from the old build which we gave a name
CMD ["./app"]
```

## Stop at a specific build stage

When you build your image, you don’t necessarily need to build the entire Dockerfile including every stage. You can specify a target build stage. The following command assumes you are using the previous `Dockerfile` but stops at the stage named `builder`:

```bash
$ docker build --target builder -t alexellis2/href-counter:latest .
```

A few scenarios where this might be very powerful are:

- Debugging a specific build stage
- Using a `debug` stage with all debugging symbols or tools enabled, and a lean `production` stage
- Using a `testing` stage in which your app gets populated with test data, but building for production using a different stage which uses real data

## **Use an external image as a “stage”[🔗](https://docs.docker.com/develop/develop-images/multistage-build/#use-an-external-image-as-a-stage)**

When using multi-stage builds, you are not limited to copying from stages you created earlier in your Dockerfile. You can use the `COPY --from` instruction to copy from a separate image, either using the local image name, a tag available locally or on a Docker registry, or a tag ID. The Docker client pulls the image if necessary and copies the artifact from there. The syntax is:

```bash
COPY --from=nginx:latest /etc/nginx/nginx.conf /nginx.conf
```

# production best pratcises