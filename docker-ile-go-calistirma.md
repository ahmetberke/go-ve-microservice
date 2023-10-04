# DOCKER İLE GO ÇALIŞTIRMA

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