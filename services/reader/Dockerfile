FROM golang:1.18
WORKDIR /go/src/github.com/PicPay/picpay-jr-devops-challenge/services/go
ADD . /go/src/github.com/PicPay/picpay-jr-devops-challenge/services/go
RUN go mod tidy
EXPOSE 8080
CMD ["go","run","main.go"]