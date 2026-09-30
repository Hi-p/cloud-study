# 1단계: 빌드 전용
FROM golang:1.22 AS builder

WORKDIR /app

COPY go.mod main.go ./

RUN CGO_ENABLED=0 go build -o server .


# 2단계: 실행 전용
FROM alpine:3.20

COPY --from=builder /app/server /server

EXPOSE 8080

CMD ["/server"]
