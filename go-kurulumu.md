# GO KURULUMU

Go'nun son sürümü veya istenilen bir sürüm için [bu sayfayı](https://go.dev/dl/) ziyaret edebilirsiniz. 

## Yükleme

```
wget https://go.dev/dl/go1.21.1.linux-amd64.tar.gz
sudo tar -xvf https://go.dev/dl/go1.21.1.linux-amd64.tar.gz
sudo mv go /usr/local
```

## Ek Ayarlar

İşletim sistemindeki root klasörüne gelin.

```
cd ~
```

Aşağıdaki yöntemlerden birisi ile `.bashrc` dosyasını açın.

### Windows Explorer ile

```
explorer.exe .
```

### Vim ile

```
vim .bashrc
```

Ardından `.bashrc` dosyasına aşağıdaki satırları ekleyin.

```
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

Burada `GOPATH` olarak yapılan tanım ile Go programlarının ayağa kaldırılacağı Go Geliştirme ortamı belirtilir.

## Kurulum ve Versiyon kontrolü

İstenilen sürümün kurulup kurulmadığını anlamak için aşağıdaki komutu kullanabilirsiniz.

```
go version
```