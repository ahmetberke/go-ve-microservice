# RESTFUL API

- [REST API Nedir?](#rest-api-nedi̇r)
- [REST API Nasıl Çalışır?](#rest-api-nasil-çalişir)
- [REST API ile Kimlik Doğrulama](#rest-api-i̇le-ki̇mli̇k-doğrulama)
- [REST API İlkeleri](#rest-api-i̇lkeleri̇)
- [Örnek REST API Tasarımı](#örnek-rest-api-tasarimi)

## REST API NEDİR?

**Rest** (**Re**presentational **S**tate **T**ransfer), Server ve Client arasında veri iletişimini sağlayan bir mimari modelidir. Rest API de Rest mimarisinin kullanımıyla web hizmetleri arasında veri iletişimini sağlayan uygulama ara birimidir. 

## REST API NASIL ÇALIŞIR?

Rest, HTTP protokolünü kullanarak, URL adresleri üzerinden veri ve dosya alışverisi sağlayan bir yapıdır. Rest API ise Rest işlemlerini yapabilmek için kurgulanmış modüle verilen isimdir. Bu API yardımıyla Rest işlemleri ve veri alışverişi yapılır.

1. İstemci, API'yi kullanarak sunucuya bir **istek** gönderir. Yani sunucu ile iletişime geçer.

2. İstemcinin, REST API'yi nasıl kullanacağı sunucu uygulamalarının **API Dökümanında** açıklanır

3. Sunucu istemcinin isteğine göre bir işlem gerçekleştirir ve bu işlem sonucunda istemciye bir dönütte bulunur.

### Rest Sırasında Kullanılan İstekler Nelerdir?

RESTful API'ler genellikle **HTTP kullanılarak** uygulanır. Bir HTTP yöntemi, sunucuya kaynağa ne yapması gerektiğini söyler. Rest sırasında kullanılan HTTP istekleri şöyledir;

- **GET:** istemciler sunucuda bulunan verileri görüntülemek ve belirli bir düzende listelemek için GET kullanılır.

- **POST**: İstemciler, verileri sunucuya eklemek için POST isteğini kullanır. Yani kaynak yaratmak için bu isteği kullanır.

- **DELETE**: Gereksiz verileri ve kaynakları silmeyi sağlar.

- **PATCH**: İstemciler, sunucudaki verilerin ve kaynakların güncellenmesi için PATCH isteğini kullanır.

## REST API İLE KİMLİK DOĞRULAMA

RESTful hizmet istemcileri, bir yanıt göndermeden önce güven oluşturmak için sunucuya **kimliğini doğrulamalıdır.** Dört kimlik doğrulama yöntemi vardır. Bunlar; **HTTP kimlik doğrulaması, temel kimlik doğrulaması, taşıyıcı kimlik doğrulaması** ve **API anahtarlarıdır.**

**HTTP kimlik doğrulaması** için temel kimlik doğrulaması ve taşıyıcı kimlik doğrulaması yöntemlerini kullanır. **Temel kimlik doğrulamasında**, istemciye kullanıcı adı ve parola gibi bilgiler gönderilir. **Taşıyıcı kimlik doğrulamasında;** istemci, kaynaklara erişebilmek için erişim denetimi bilgilerini gönderir. 

**API anahtarları** ise **REST API kimlik doğrulaması** için kullanılan başka bir yöntemdir. Bu yöntemde, sunucu ilk kez istekte bulunan istemciye bir API anahtarı iletir ve  istemci de verilere erişebilmek için bu API anahtarını kullanır. 

## REST API İLKELERİ

### İstemci - Sunucu Özerkliği

REST API yürütme, yürütme ve sunucu birbirinden bağımsız olarak çalışır. İstemci ve sunucu bu sırada farklı teknoloji yüklemelerini kullanır. İstemci ve sunucunun bağımsız birbirinden olması sistem yapılarını ve güvenirliğini artırır.

### Tek Tip Arayüz

REST API'nin tek tip olarak kullanılmasına sahip olması kullanım kolaylığı sağlamasının yanında ortak bir standart anlayış sağlar. Yani yürütme ve sunucu iletişim kurarken standartlaştırılmış bir iletişim şekli vardır. Bu özellik REST API'yi REST olmayan API'lerden ayıran önemli bir özelliktir. 

### Katmanlı Mimari

REST API sistemi, bağlantılı bir yapıya sahiptir. Her katman diğer katmandan bağımsız olarak çalışır. Bu bağlantı mimarisinde, ele alınan ve sunucuda bulunan bir proxy yerleştirilerek ölçeklendirilebilirlik artırılır. Ayrıca sistem güvenliği için ayrı bir katman oluşturularak, sistemin daha güvenli hale getirilmesi sağlanır. 

### Önbellekleme

REST API'ler, önbellekleme işlemi yaparak verileri tekrar tekrar istemek yerine önbellekte depolar. Bu da sunucu üzerindeki yükü hafifleterek, uygulama yürütme dizisini sağlar.

### Durum Bilgisinin Olmaması

REST API, katmanlardan oluşur ve bu katmanlardan bağımsızdır. Ayrıca önceki kayıtlarla ilgili herhangi bir depolama depoları. Her bir oturum ve istek önceki oturumlardan bağımsız olarak ele alınır. 

### İsteğe Bağlı Kod Yapısı

REST API'nin çizimine bağlı kod yapısı bulunur. Bu COD olarak da adlandırılır. KOİ, hayvanların özelliklerini daha fazla kontrol edebilmesini sağlar. Örneğin, bir web sitesinde kayıt/başvuru formülü doldururken bilgileri yanlış girdiğinizde tarayıcı bu hatalı fark eder ve uyarı verir. Bu özellik, sunucu tarafından işletim kodu sayesinde yapılır.

## ÖRNEK REST API TASARIMI

Rest API kavramını daha iyi anlamak için, sadece URL adreslerini, bu adreslerin neler yaptığını ve sunucuya gönderip sunucudan alacağımız json objelerini belirleyeceğimiz bir örnek yapacağız.

Örneğimizde bir **TODO** uygulamasının Rest API mimarisini tasarlayacağız. 

Todo Objesinin sunucu veya bir veritabanında tutulduğunu farzedelim. Bu objenin aşağıdaki gibi özellikleri olacaktır;

- **ID** : Todo'nun eşsiz kimliğini (ID) tutar, tipi ***number'dır***
- **Title** : Todo'nun başlığını tutar, tipi ***string'tir.***
- **Content** : Todo'nun detaylarını tutar, tipi ***string'tir.***
- **IsDone** : Todo'nun yapılıp yapılmadığını tutar, tipi ***boolean'dir.***

Sunucunun **POST** methodu için kabul edeceği JSON objesi aşağıdaki gibi olmalıdır.

```json
{
  "title" : "string",
  "content" : "string",
}
```

Sunucudan dönen cevaplar aşağıdaki gibi olmalıdır.
**status** alanı sunucudan dönen cevabın durumunu belirler. **message** alanı ise sunucudan bir mesaj döndüyse bu mesajı tutar.

```json
{
  "status" : "success|fail|error",
  "message" : "string",
  "data" : {
    "id" : "number",
    "title" : "string",
    "content" : "string",
    "is_done" : "boolean"
  }
}
```

Bu API'nin tüm url adresleri ve işlevleri aşağıdaki tablodaki gibi olmalıdır.

|URL|Method|İşlev|
|---|------|-----|
|/todo|GET|Sunucuda veya veritabanında bulunan tüm **todo**'ların listesinin döndürülmesini sağlar.|
|/todo/{id}|GET|**ID** kullanılarak belirli bir öbjenin döndürülmesini sağlar.|
|/todo|POST|Yeni bir todo objesinin oluşturulmasını sağlar. Gönderilen isteğin **body**'sinde Todo'nun JSON Objesi bulunmalıdır.|
|/todo/{id}|DELETE|**ID** kullanılarak belirli bir objenin sunucudan veya veritabanından silinmesini sağlar.|
|/todo/{id}|PATCH|**ID** kullanılarak belirli bir objenin güncelleştirilmesini sağlar. **body** içerisinde objenin yeni alanları yer almalıdır.|