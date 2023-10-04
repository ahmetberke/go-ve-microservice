# DOCKER İLE GO ÇALIŞTIRMA

- [Docker Nedir](#docker-nedir)
- [Docker Compose Nedir](#docker-compose-nedir)
- [WSL2 Ubuntu'da Docker Kurulumu](#wsl2-ubuntuda-docker-kurulumu)
- [Go İle Örnek Uygulama](#go-ile-örnek-uygulama-ayağa-kaldırma)

## Docker Nedir

Docker, Open Source bir **container** teknolojisidir. Docker, aynı işletim sistemi üzerinde, yüzlerce hatta binlerce birbirinden izole ve bağımsız containerlar sayesinde sanallaştırma sağlayan bir teknolojidir.

Microservice'ler için olmazsa olmaz container teknolojilerinin başında gelen Docker, microservicelerin kolay ayağa kaldırılmasını ve birbirinden izole olmasını sağlar.

Docker'ı öğrenmek için [kendi dökümanından](https://docs.docker.com/) veya internette bulunan kaynaklardan yararlanabilirsiniz.

## Docker Compose Nedir?

Docker Compose, birden fazla containera sahip uygulamaları tanımlamak ve ayağa kaldırmak için kullanılır. Compose ile birlikte uygulamalarınızı configüre etmek veya dışarıdan ulaşılan imageleri dahil etmek için bir YAML dosyası kullanılır. Ardından sadece bir kaç komutla bu YAML dosyası kullanılarak tüm servisler yönetilir.

Daha fazla bilgi almak için Docker Compose'ın [kendi dökümanını](https://docs.docker.com/compose/) okuyabilirsiniz.

## WSL2 Ubuntu'da Docker Kurulumu

### 1. Apt Repository Kurulumu

```
# Resmi Docker GPG Anahtarı ekleme:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Repository'i Apt Kaynaklarına ekleme:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

### Docker Paketlerini Yükleme

Son versiyonu indirmek için;

```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Docker Engine'in doğru kurulduğunu kontrol etmek için;

```
sudo docker run hello-world
```

Bu işlemlerden sonra Docker Engine, CLI, Compose vb.nin kurulumu tamamlanacaktır.

## Go ile Örnek Uygulama Ayağa Kaldırma

Aşağıdaki Örnek **echo** frameworkü kullananan bir "hello-world" API'dir.

Bu uygulama `PORT` olarak tanımlanan ortam değişkenini TCP Port olarak kullanmaktadır. Eğer herhangi bir yoksa varsayılan olarak `8080` portu dinlenmektedir.

Aşağıdaki uygulamayı git ile klonlayarak `go/src/github.com/user/` dizinimize çekiyoruz.

```
git clone https://github.com/docker/docker-gs-ping
```

```go
package main

import (
	"net/http"
	"os"

	"github.com/labstack/echo/v4"
	"github.com/labstack/echo/v4/middleware"
)

func main() {

	e := echo.New()

	e.Use(middleware.Logger())
	e.Use(middleware.Recover())

	e.GET("/", func(c echo.Context) error {
		return c.HTML(http.StatusOK, "Hello, Docker! <3")
	})

	e.GET("/health", func(c echo.Context) error {
		return c.JSON(http.StatusOK, struct{ Status string }{Status: "OK"})
	})

	httpPort := os.Getenv("PORT")
	if httpPort == "" {
		httpPort = "8080"
	}

	e.Logger.Fatal(e.Start(":" + httpPort))
}

// Simple implementation of an integer minimum
// Adapted from: https://gobyexample.com/testing-and-benchmarking
func IntMin(a, b int) int {
	if a < b {
		return a
	}
	return b
}
```

### Uygulama'nın Çalışma Testi

Docker ile uygulamayı ayağı kaldırmadan önce doğru çalıştığını go ile test ediyoruz.

```
go run main.go
```

Uygulama çalıştıktan sonra başka bir terminal açıp aşağıdaki komutla birlikte uygulama bir istekte bulunuyoruz.

```
curl localhost:8080
```

Dönüt:

```
Hello, Docker <3
```

### Docker ile Ayağa Kaldırma

Uygulamayı Docker ile ayağa kaldırmak için öncelikle uygulama dizinin bir `Dockerfile` dosyası oluşturuyoruz. Eğer klonla birlikte zaten gelmişse süreç açısından o dosyayı silip tekrardan yeni bir `Dockerfile` dosyası oluşturunuz.

Kullanılacak Image ve versiyonu belirleniyor. Localde kullandığınız versiyon ile docker'a verdiğiniz versiyonun aynı olması önemli.

``` Dockerfile
FROM golang:1.21
```

Projenin çalıştırılacağı ortam için bir klasör tanımı yapılıyor.

```Dockerfile
WORKDIR /app
```

Go Modüllerini barındıran `go.mod` ve `go.sum` dosyaları uygulama klasörüne eklenip indiriliyor.

```Dockerfile
COPY go.mod go.sum
RUN go mod download
```

Kaynak kodun tamamı uygulama klasörüne kopyalanıyor

```Dockerfile
COPY *.go ./
```

Uygulamanın çalıştırılabilir hale gelmesi için build alınıyor.

```Dockerfile
RUN CGO_ENABLED=0 GOOS=linux go build -o /docker-gs-ping
```

Uygulamanın tespit etmesi için TCP Port'u `PORT` adında ortam değişkeni olarak tanımlanıyor.

```Dockerfile
EXPOSE 8080
```

Uygulama çalıştırılıyor.

```Dockerfile
CMD ["/docker-gs-ping"]
```

Dockerfile'ın tamamı aşağıdaki gibi

```Dockerfile
# syntax=docker/dockerfile:1

FROM golang:1.19

# Set destination for COPY
WORKDIR /app

# Download Go modules
COPY go.mod go.sum ./
RUN go mod download

# Copy the source code. Note the slash at the end, as explained in
# https://docs.docker.com/engine/reference/builder/#copy
COPY *.go ./

# Build
RUN CGO_ENABLED=0 GOOS=linux go build -o /docker-gs-ping

# Optional:
# To bind to a TCP port, runtime parameters must be supplied to the docker command.
# But we can document in the Dockerfile what ports
# the application is going to listen on by default.
# https://docs.docker.com/engine/reference/builder/#expose
EXPOSE 8080

# Run
CMD ["/docker-gs-ping"]
```

#### Image'i Build Alma

Hazırlanan `Dockerfile`'a göre uygulamayı build ediyoruz.
Build için `docker build` komutu kullanılır. Bu komut parametre olarak `Dockerfile`'nin bulunduğu klasörü alır. `Dockerfile` tespitini otomatik yaparak uygulamayı build eder.

Opsiyonel olarak aldınan builde bir isim/tag vermek için `--tag` flag'i kullanılır.

```
docker build --tag docker-gs-ping .
```

#### Local Image'leri Görüntüleme

Build aldığınız tüm docker image'lerini görüntüleyebilirsiniz.

```
docker image ls
```