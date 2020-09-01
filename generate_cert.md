# generate cert

## mkcert

尝试了一圈，因为`Common Name`过时了，需要用SAN，但是用openssl生成都没有成功。最终放弃，使用mkcert创建。

```bash
// CA
$ mkcert $(mkcert -CAROOT)/rootCA.pem
// server
$ mkcert server.io
// client
$ mkcert -client client.io
```

## CA root

```bash
$ openssl genrsa -out ca.key 2048
$ openssl req -new -x509 -days 3650 \
    -subj "/C=GB/L=China/O=gobook/CN=github.com" \
    -key ca.key -out ca.crt
$ openssl req -new -x509 -days 3650 \
    -config mysan.cnf \
    -key ca.key -out ca.crt
```

## server

```bash
$ openssl genrsa -out server.key 2048
$ openssl req -new \
    -subj "/C=GB/L=China/O=server/CN=server.io" \
    -key server.key \
    -out server.csr
$ openssl req -new \
    -key server.key \
    -out server.csr \
    -config mysan.cnf
$ openssl x509 -req -sha256 \
    -CA ca.crt -CAkey ca.key -CAcreateserial -days 3650 \
    -in server.csr \
    -out server.crt
```

## client

```bash
$ openssl genrsa -out client.key 2048
$ openssl req -new \
    -subj "/C=GB/L=China/O=client/CN=client.io" \
    -key client.key \
    -out client.csr
$ openssl req -new \
    -key client.key \
    -out client.csr \
    -config mysan.cnf
$ openssl x509 -req -sha256 \
    -CA ca.crt -CAkey ca.key -CAcreateserial -days 3650 \
    -in client.csr \
    -out client.crt
```
