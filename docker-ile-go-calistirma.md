# DOCKER İLE GO ÇALIŞTIRMA

- [Docker Nedir](#docker-nedir)
- [WSL2 Ubuntu'da Docker Kurulumu](#wsl2-ubuntuda-docker-kurulumu)
- [Örnek Uygulama](#go-ile-örnek-uygulama-ayağa-kaldırma)
- [Dockerfile Nedir, Nasıl Kullanılır](#dockerfile-nedir-nasıl-kullanılır)
- [Build Alma](#build-alma)
- [Uygulamayı Çalıştırma](#uygulamayı-çalıştırma)
- [Docker Compose Nedir](#docker-compose-nedir)
- [Docker Compose Kullanımı](#docker-compose-kullanımı)

## Docker Nedir

Docker, Open Source bir **container** teknolojisidir. Docker, aynı işletim sistemi üzerinde, yüzlerce hatta binlerce birbirinden izole ve bağımsız containerlar sayesinde sanallaştırma sağlayan bir teknolojidir.

Microservice'ler için olmazsa olmaz container teknolojilerinin başında gelen Docker, microservicelerin kolay ayağa kaldırılmasını ve birbirinden izole olmasını sağlar.

Docker'ı öğrenmek için [kendi dökümanından](https://docs.docker.com/) veya internette bulunan kaynaklardan yararlanabilir.

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

Aşağıdaki uygulamayı git ile klonlayarak `go/src/github.com/user/` dizinine klonlanır.

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

Docker ile uygulamayı ayağı kaldırmadan önce doğru çalıştığı go ile test edilir.

```
go run main.go
```

Uygulama çalıştıktan sonra başka bir terminal açıp aşağıdaki komutla birlikte uygulamaya bir istekte bulunulur.

```
curl localhost:8080
```

Dönüt:

```
Hello, Docker <3
```

## Dockerfile Nedir Nasıl Kullanılır

Dockerfile,belli bir image görüntüsü oluşturmak için var olan tüm **katmanların** açıklandığı **uzantısı olmayan dosyadır.** Dockerfile içerisinde hangi Image’ın kullanılacağı, hangi dosyaları içereceği ve hangi uygulamanın hangi parametrelerle çalışacağı yazılır. Docker, Dockerfile dosyasında bulunan komutları sırayla tek tek çalıştırır. Her komut yeni bir katman oluşturur. Docker **build** sonunda elimizde uygulaması ait Docker Image oluşur .Docker Image container oluşturarak uygulaması ayağa kaldırabiliriz. Bilinmesi gereken en önemli nokta ise dosya adının kesinlikle Dockerfile şeklinde olmasıdır. “dockerfile”, “DockerFile” gibi verilen isimlendirmeler yanlıştır.

Aşağıda Dockerfile'ın nasıl kullanılacağı, yukarıdaki örnek üzerinden anlatılmıştır.

Uygulamayı Docker ile ayağa kaldırmak için öncelikle uygulama dizinin bir `Dockerfile` dosyası oluşturulur. Eğer klonla birlikte zaten gelmişse süreç açısından o dosyayı silip tekrardan oluşturulur.

Kullanılacak Image ve versiyonu belirlenir. Localde kullanılan versiyon ile docker'a verilen versiyonun aynı olması önemlidi
r.

``` Dockerfile
FROM golang:1.21
```

Projenin çalıştırılacağı ortam için bir klasör tanımı yapılır.

```Dockerfile
WORKDIR /app
```

Go Modüllerini barındıran `go.mod` ve `go.sum` dosyaları uygulama klasörüne eklenip indirilir.

```Dockerfile
COPY go.mod go.sum
RUN go mod download
```

Kaynak kodun tamamı uygulama klasörüne kopyalanır.

```Dockerfile
COPY *.go ./
```

Uygulamanın çalıştırılabilir hale gelmesi için build alınır.

```Dockerfile
RUN CGO_ENABLED=0 GOOS=linux go build -o /docker-gs-ping
```

Uygulamanın tespit etmesi için TCP Port'u `PORT` adında ortam değişkeni olarak tanımlanır.

```Dockerfile
EXPOSE 8080
```

Uygulama çalıştırılır.

```Dockerfile
CMD ["/docker-gs-ping"]
```

**Dockerfile**'ın tamamı aşağıdaki gibidir

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

## Build Alma

Hazırlanan `Dockerfile`'a göre uygulama build edilir.
Build için `docker build` komutu kullanılır. Bu komut parametre olarak `Dockerfile`'nin bulunduğu klasörü baz alıp `Dockerfile` tespitini otomatik yaparak uygulamayı build eder.

Opsiyonel olarak aldınan builde bir isim/tag vermek için `--tag` flag'i kullanılır.

```
docker build --tag docker-gs-ping .
```

### Local Image'leri Görüntüleme

Build alınan tüm docker **image**'leri görüntülenir.

```
docker image ls
```

## Uygulamayı Çalıştırma

Build alınan İmage'in doğru şekilde nasıl çalıştırılacağı anlatacağız. 

Bir image'i içeren containerı çalıştırmak için `docker run` komutunu kullanırız. Bu komut parametre olarak çalıştırılacak olan image'in ismini alır.

 ```
 docker run docker-gs-ping
 ```

 Eğer bu şekilde çalıştırılacak uygulamaya bir istekte bulunursak.

 ```
 curl http://localhost:8080
 ```

 ```
 curl: (7) Failed to connect to localhost port 8080: Connection refused
 ```

 Bu hatanın sebebi container'a verdiğimiz port uygulamanın o container içerisinden erişilebilir portudur. Fakat bizim `curl` ile attığımız istek uygulamaya contanier dışından geliyor bundan dolayı uygulama aslında container dışından bağlanılabilir değil oluyor. 
 
 Bunu düzeltmek için container'ı çalıştırırken dışarıya paylaşacağı portu da belirtmek gerekiyor. `--publish` flagi ile birlikte (kısa hali `--p` dir) `[host_port]:[container-port]` verilerek uygulama yayınlanmış oluyor.

```
docker run --publish 8080:8080 docker-gs-ping
```

Tekrardan `curl` ile istek atıldığında doğru yanıtı alacağız.
	
## Docker Compose Nedir?

Docker Compose, birden fazla containera sahip uygulamaları tanımlamak ve ayağa kaldırmak için kullanılır. Compose ile birlikte uygulamalarınızı configüre etmek veya dışarıdan ulaşılan imageleri dahil etmek için bir YAML dosyası kullanılır. Ardından sadece bir kaç komutla bu YAML dosyası kullanılarak tüm servisler yönetilir.

Daha fazla bilgi almak için Docker Compose'ın [kendi dökümanınından](https://docs.docker.com/compose/) faydalanabilirsiniz.

## Docker Compose Kullanımı

Docker Compose kullanırken, Birden fazla uygulamanın nasıl build edileceği, yönetileceği ve çalıştırılacağını belirlemek için bir `.yaml` dosyası oluşturuyoruz.

`docker-compose.yaml` olarak oluşturduğumuz dosyada ilk olarak **compose**'un versinunu belirliyoruz.

```yaml
version: "3.9"	
```

Tanımlanacak servisleri `services` karşısında bir liste olarak tanımlıyoruz. Biz burada kendi servisimizi `go-app` tagi ile tanımlayacağız.

Ardından bu servisin nasıl build edileceğini `build` tagi ile tanımlıyoruz. `build`in içerisinde `dockerfile` ile **Dockerfile** dosyazımızın ismimi, `context` ile de proje klasörünün uzantısını belirliyoruz.

```yaml
services:
  go-app:
    build:
      dockerfile: Dockerfile
      context: .
```

Container'ın dışarıya hangi **port**'tan paylaşılacağını `ports` ile belirliyoruz.

```yaml
services:
  go-app:
    ...
    ports:
      - "8080:8080"
```

**Container**'ın üzerinde bulunan dosyaları saklayacağımız uzantıyı `volumes` ile belirliyoruz.

```yaml
services:
  go-app:
	  ...
    volumes:
      - .:/opt/app/api
```

**Compose** dosyamızın tamamı aşağıdadır;

```yaml
version: "3.9"

services:
  go-app:
    build:
      dockerfile: Dockerfile
      context: .
    ports:
      - "8080:8080"
    volumes:
      - .:/opt/app/api
```