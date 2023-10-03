# GO İLE MİCROSERVİCE TEMELLERİ

- [Gereksinimler](#gereksinimler)
- [Go Kurulumu](#go-kurulumu)
- [Docker ile Go Çalıştırma]()
- [RESTful API Nedir]()
- [Gin Framework'ü Nedir ve Nasıl Kullanılır]()
- [Microservice Nedir ve Monolitich ile Farkları Nelerdir]()
- [Go ile Microservice'e Giriş]()
- [RESTful API Microservice Geliştirme]()
- [Microservice'te Veritabanı Kullanımı (MongoDB)]()
- [Microservice'te Cache Mekanizması Kullanımı (Redis)]()
- [Microservice'te Mesaj Kuyruğu Servisi Kullanımı (RabbitMQ)]()

## Gereksinimler

Bu dökümanda her şey WSL tabanlı Ubuntu OS üzerinden anlatılacaktır. Geliştirme ortamı Ubuntu ise WSL kurulumuna ihtiyaç yoktur.  Eğer Windows ise [bu bağlantıdan](https://www.evrenbal.com/wsl-2-kurulumu-6-kolay-adim/) adım adım cihazınıza WSL2 Ubuntu kurulumu yapabilirsiniz.

## Go Kurulumu

Go'nun son sürümü veya istenilen bir sürüm için [bu sayfayı](https://go.dev/dl/) ziyaret edebilirsiniz. 

### Yükleme

`wget https://go.dev/dl/go1.21.1.linux-amd64.tar.gz`
`sudo tar -xvf https://go.dev/dl/go1.21.1.linux-amd64.tar.gz`
`sudo mv go /usr/local`

### Ek Ayarlar

İşletim sistemindeki root klasörünü gelin.

`cd ~`

Aşağıdaki yöntemlerden birisi ile `.bashrc` dosyasını açın.

#### Windows Explorer ile

`explorer.exe .`

#### Vim ile

`vim .bashrc`

Ardından `.bashrc` dosyasına aşağıdaki satırları ekleyin.

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

### Kurulum ve Versiyon kontrolü

İstediğiniz sürümün kurulup kurulmadığını anlamak için aşadıkai komutu kullanın.

`go version`