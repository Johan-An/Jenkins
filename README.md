# Official Jenkins Docker image

The Jenkins Continuous Integration and Delivery server.

This is a fully functional Jenkins server, based on the Long Term Support release.
[http://jenkins.io/](http://jenkins.io/).

For weekly releases check out [`jenkinsci/jenkins`](https://hub.docker.com/r/jenkinsci/jenkins/)


<img src="http://jenkins-ci.org/sites/default/files/jenkins_logo.png"/>


# Usage

```
docker compose up -d
```

# Generate certs
## 1.1 创建证书颁发机构（CA）
```
mkdir -p certs && cd certs
# 生成 CA 私钥
openssl genrsa -out ca-key.pem 4096

# 创建 CA 证书
openssl req -new -x509 -days 365 -key ca-key.pem -sha256 -out ca.pem -subj "/CN=localhost"
```
## 1.2 生成服务器证书
```
# 生成服务器私钥
openssl genrsa -out server-key.pem 4096

# 创建服务器证书签名请求（CSR）
openssl req -new -key server-key.pem -out server.csr -subj "/CN=localhost"

# 创建扩展文件
echo "subjectAltName = DNS:localhost,IP:127.0.0.1" > extfile.cnf

# 使用 CA 签署服务器证书
openssl x509 -req -days 365 -in server.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out server-cert.pem -extfile extfile.cnf

```

## 1.3 生成客户端证书
```
# 生成客户端私钥
openssl genrsa -out key.pem 4096

# 创建客户端证书签名请求（CSR）
openssl req -new -key key.pem -out client.csr -subj "/CN=client"

# 创建扩展文件
echo "extendedKeyUsage = clientAuth" > extfile.cnf

# 使用 CA 签署客户端证书
openssl x509 -req -days 365 -in client.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out cert.pem -extfile extfile.cnf

```

现在你应该有以下文件：
ca.pem
server-key.pem
server-cert.pem
key.pem
cert.pem
将这 5 个文件放到Jenkins项目根目录的/certs/下面