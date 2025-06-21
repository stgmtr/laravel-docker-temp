<p align="center"><a href="https://stgm.org.tr" target="_blank"><img src="https://www.stgm.org.tr/themes/custom/stgm/images/logo/stgm_logo_tr.png" width="400" alt="STGM Logo"></a></p>


# Laravel Docker Yapılandırma Şablonu

Bu şablon ölçeklenebilir bir web uygulaması iskeletidir. Kolay bir kurulum ve esnek bir çalışma ortamı sunarak, STK'ların dijitalleşme süreçlerine katkı sağlamayı ve temel web ihtiyaçlarını karşılamalarına yardım etmeyi amaçlar.

## Kullanılan Teknolojiler ve Gereksinimler

PHP 8.3 (Laravel 12), Nginx, MySQL, Docker. Bu projeyi yerel makinenizde kullanabilmeniz için [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) ve [Docker Desktop](https://www.docker.com/products/docker-desktop/) kurulu olmalıdır.

## Kurulum Adımları

##### 1. Repoyu Klonlayın
```
git clone git@github.com:stgmtr/laravel-docker-temp.git <DIZIN_ADI>
cd DIZIN_ADI
```

##### 2. İkinci öğe
Projenin kök dizininde `.env` dosyanızı oluşturun. Bunun için proje ile birlikte gelen `.env.example` dosyasını kopyalayabilirsiniz.
```
cp .env.example .env 
```

`.env` dosyasının içeriğini kendi verilerinizle değiştirin. Buradaki bilgiler Docker container'larının kurulumu sırasında kullanılacaktır.
```
IMAGE_NAME=laravel-docker

# Nginx
NGINX_HOST=test.stgm.org.tr
NGINX_PORT=40085

MYSQL_PORT=8989

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=testdatabase
DB_USERNAME=testuser
DB_PASSWORD=test1234

MYSQL_ROOT_PASSWORD=VRTxbbLUiSZwtiA
```

##### 3. Docker Container'ını başlatın
Bu komut ile Docker imajları oluşacak (ilk seferde) ve servisler başlayacaktır.
```
docker-compose up --build -d
```

##### 4. Laravel .env ayarlarını yapın ve migration çalıştırın
Kurulum sırasında `/web` dizininde bulunan `.env.example` üzerinden bir `.env` dosyası oluşturulacaktır. Buradaki bilgileri kendi ayarlarınızla değiştirin. Ardından veri tabanı tablolarınızı oluşturun.
```
docker-compose exec app php artisan migrate --force
```
##### 5. Storage Link oluşturun
Laravel uygulamasının kullanıcı tarafından yüklenen dosyalara sağlıklı bir şekilde erişebilmesi için sembolik bağlantı oluşturun.
```
docker-compose exec app php artisan storage:link
```

## Dizin Yapısı

Projenin dizin yapısı aşağıdaki gibidir.

* `web/`: Laravel uygulamasının tüm kodları bu dizin altındadır.
* `etc/`: Dockerfile, Nginx yapılandırması ve PHP ayarları gibi yardımcı dosyaları içerir.
    * `etc/Dockerfile`: PHP-FPM konteyneri için Dockerfile.
    * `etc/nginx/default.conf`: Nginx ana yapılandırma dosyası.
    * `etc/nginx/default.template.conf`: Nginx için `.env` değişkenlerini kullanan şablon dosyası.
    * `etc/php/php.ini`: PHP çalışma zamanı ayarları.
* `data/db/`: MySQL veritabanı verilerinin saklandığı dizin (kalıcı depolama).
* `docker-compose.yml`: Docker Compose yapılandırma dosyası
* `example.env`: `.env` için örnek dosya

## İhtiyaç duyulabilecek docker komutları

* Tüm Konteynerleri Durdurma ve Kaldırma:
```
docker-compose down
```

* Tüm Konteynerleri, Volumeleri ve İmajları Tamamen Temizleme (Dikkat! Veriler silinebilir):
```
docker-compose down -v --rmi all
docker system prune -a --volumes # Kullanılmayan tüm Docker kaynaklarını siler
```

* PHP Konteynerine Bağlanma (Bash Shell Açma):
```
docker-compose exec app bash
```

* Laravel Önbelleklerini Temizleme
```
docker-compose exec app php artisan optimize:clear
```

* Laravel Loglarını Görüntüleme
```
docker-compose exec app tail -f storage/logs/laravel.log
```
