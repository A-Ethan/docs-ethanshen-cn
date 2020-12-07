# Golang Demo

github: https://github.com/A-Ethan/golang-demo

## 命令

```bash
# clone
git clone https://github.com/A-Ethan/golang-demo.git
# build
docker build -f Dockerfile -t=<image:tag>
```

## Dockerfile

```Dockerfile
FROM uhub.service.ucloud.cn/hello123/golang:1.13 as builder
ENV GOPROXY https://goproxy.cn,direct
ENV GO111MODULE on
ADD ./ /go/src/github.com/A-Ethan/golang-demo
WORKDIR /go/src/github.com/A-Ethan/golang-demo
RUN go mod tidy
RUN CGO_ENABLED=0 GOOS=linux go build main.go
EXPOSE 80
CMD ["./main"]
```