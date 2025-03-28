## SANAL MAKİNEYİ BAŞLAT / MAC Terminali Aç

VirtualBox'a gidin ve centos_min/rocky_linux sanal makinesini başlatın (headless start).

MobaXterm'i açın ve centos_min/rocky_linux bağlantısını kurun.

veya docker desktop kurun
Docker yükleyin. cheatsheete bakınız.

### 1. Docker Versiyonunu Kontrol Etme

- İlk olarak, Docker’ın doğru kurulduğunu ve çalıştığını kontrol edelim:

```bash
docker --version
```
Bu komut Docker'ın kurulu olan sürümünü gösterir.

Output
```
(base) []$ docker --version
Docker version 26.1.3, build b72abbb
```

### 2. Hazır İmajları Kullanarak Konteyner Oluşturma

Docker Hub’da bulunan hazır imajları kullanarak kolayca konteyner oluşturabilirsiniz.

#### Örnek: **Nginx Web Sunucusu Oluşturma**

1. Docker Hub’dan bir Nginx imajı çekme:

```bash
docker pull nginx
```

2. Bu imajı kullanarak bir konteyner başlatma:

•  -d: Konteyneri arka planda çalıştırır.

•  -p 8080:80: 8080 portunu dışarıya açar ve 80 portunu konteynerde kullanır.

```bash
docker run -d -p 8080:80 nginx
```
   
Output
```
(base) []$ docker run -d -p 8080:80 nginx
a411335f7a80e2a030a08aefd03c056374113461e5c7f3e68994f1131d4234c9
```

3. Çalışan konteyneri kontrol etmek için:
```bash
docker ps
```
 
Output
```
(base) []$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                   NAMES
a411335f7a80   nginx     "/docker-entrypoint.…"   3 seconds ago   Up 3 seconds   0.0.0.0:8080->80/tcp, :::8080->80/tcp   intelligent_euclid
```


4. Tarayıcıda `localhost:8080` adresine giderek Nginx’in çalıştığını görebilirsiniz.

5. Konteyneri durdurmak için:
Konteyneri durdurduğunuzda, port bağlantısı da duracaktır. Mevcut konteyneri durdurmak için konteyner ID'sini veya adını kullanabilirsiniz.
Bu komut, Nginx'in çalıştığı konteyneri durdurur ve 5000 portu üzerindeki bağlantı kesilir.

```bash
docker stop container_id
```
 
Output
```
(base) []$ docker stop a411335f7a80
a411335f7a80
```

Durdurduk ama silmedik. Kontrol için 
```bash
docker ps -a 
```
```
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS                          PORTS     NAMES
a411335f7a80   nginx     "/docker-entrypoint.…"   6 minutes ago   Exited (0) About a minute ago             intelligent_euclid
```

6. Konteyneri silmek için:
Port bağlantısını tamamen sonlandırmak ve konteyneri silmek için:

```bash
docker rm container_id
```
   
Output
```
(base) []$ docker rm  a411335f7a80
a411335f7a80
```

Bu, konteyneri durdurup sildikten sonra port bağlantısı tamamen sonlandırılır.

7. İmage silmek için:
```bash
docker image rm image_name
```
Output
```
(base) [train@trainvm data101]$ docker image rm nginx
Untagged: nginx:latest
Untagged:nginx@sha256:42e917aaa1b5bb40dd0f6f7f4f857490ac7747d7ef73b391c774a41a8b994f15
Deleted:sha256:f876bfc1cc63d905bb9c8ebc5adc98375bb8e22920959719d1a96e8f594868fa
Deleted:sha256:e0f1c40b04bce92241b6a81812e29990b5ff711bfd3fb6817e4ec03f3f09cb72
Deleted:sha256:f3764bf5781131fa5df6d78bcde2d5905c7d2451b7084ff73d6cbb679fa2a573
Deleted:sha256:d0516283d34466f5e50f48966ce9c304cb665bd9a6fe984ca21166ff6d519264
Deleted:sha256:bf29edbdcdd853fb80ac4623db054796c3050017aa68dd79958ab61762fa85dc
Deleted:sha256:344c966b8cc1774f55cf5f6fb3c438c497a2a84d4e9e09befc7e1623f97029bf
Deleted:sha256:59db063f63f68b942f3c60769828c15efe9abd12362d5c6d925a0484bbf031d0
Deleted:sha256:8b296f48696071aafb5a6286ca60d441a7e559b192fc7f94bb63ee93dae98f17
```

8. İmage listelemek için:

```bash
docker images
```
   
Output
```
(base) []$ docker images
REPOSITORY                          TAG                            IMAGE ID       CREATED         SIZE
postgres                            15                             4ff1e3b62d7d   7 weeks ago     426MB
registry.k8s.io/git-sync/git-sync   v4.2.3                         1384285ff8a3   8 months ago    57.4MB
apache/airflow                      2.7.1                          f37575f80db5   13 months ago   1.51GB
minio/minio                         RELEASE.2023-06-02T23-17-26Z   b1241b0087e5   19 months ago   263MB
nginx                               1.23                           a7be6198544f   20 months ago   142MB
mysql                               8.0.31                         7484689f290f   2 years ago     538MB
postgres                            10                             1cad456b3a24   2 years ago     202MB
```

9. Docker Image komutları hakkında bilgi almak için:

```bash
docker image --help
```

## 3. Basit bir uygulama ile kendi imajını oluşturma:
- Yeni bir folder oluşturma:
```bash
mkdir my-first-dockerfile
```
- Dosyaları oluşturma:

```bash
touch app.py
```

```bash
touch Dockerfile
```

```bash
touch requirements.txt
```

```bash
ls -lh
```
Output
```
(base) []$ ls -lh
total 12K
-rw-rw-r--. 1 train train 171 Jan 13 13:56 app.py
-rw-rw-r--. 1 train train 461 Jan 13 13:56 Dockerfile
-rw-rw-r--. 1 train train  29 Jan 13 13:56 requirements.txt
```
 Aşağıdaki içeriği kopyalayıp app.py dosyasına yapıştırın.

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')

```

- Aşağıdaki içeriği kopyalayıp requirements.txt dosyasına yapıştırın.

```txt
Flask==2.0.2
Werkzeug==2.0.2
```

- Aşağıdaki içeriği kopyalayıp Dockerfile dosyasına yapıştırın.

```dockerfile
# Python imajını kullan
FROM python:3.9

# Konteyner içinde çalışma dizinini /app olarak ayarla
WORKDIR /app

# Geçerli dizindeki tüm dosyaları konteynerde /app dizinine kopyala
COPY . /app

# requirements.txt içinde belirtilen bağımlılıkları yükle (önbelleğe almadan)
RUN pip install --no-cache-dir -r requirements.txt

# 5000 portunu bu konteynerin dışındaki dünyaya aç
EXPOSE 5000

# Konteyner başlatıldığında app.py dosyasını çalıştır
CMD ["python", "app.py"]

```

### Image oluşturma ve Container çalıştırma:

- Image oluşturma:

* -t , Docker imajına bir isim (tag) vermek için kullanılır.
* içinde bulunduğunuz dizindeki (.) Dockerfile’ı kullanarak bir imaj oluşturur ve bu imaja my-flask-app adını (etiketini) verir.

```bash
docker build -t my-flask-app .
```
- Image kotnrol etme:

```
(base) []$ docker images
REPOSITORY                          TAG                            IMAGE ID       CREATED              SIZE
my-flask-app                        latest                         33c1d69eec53   About a minute ago   1.01GB
postgres                            15                             4ff1e3b62d7d   7 weeks ago          426MB
registry.k8s.io/git-sync/git-sync   v4.2.3                         1384285ff8a3   8 months ago         57.4MB
apache/airflow                      2.7.1                          f37575f80db5   13 months ago        1.51GB
minio/minio                         RELEASE.2023-06-02T23-17-26Z   b1241b0087e5   19 months ago        263MB
nginx                               1.23                           a7be6198544f   20 months ago        142MB
mysql                               8.0.31                         7484689f290f   2 years ago          538MB
postgres                            10                             1cad456b3a24   2 years ago          202MB
```

- Konteyneri çalıştırma ve uygulamayı erişilebilir hale getirme:

```bash
docker run -p 5050:5000 -d my-flask-app 
```

Output
```
(base) []$ docker run -p 5000:5000 -d my-flask-app
ce44578766c2af96c4842acfc1b1358675cedbd48eed1693ca147efb94adf02c
```
```
(base) []$ docker ps -a
CONTAINER ID   IMAGE          COMMAND           CREATED          STATUS          PORTS                                       NAMES
ce44578766c2   my-flask-app   "python app.py"   20 seconds ago   Up 19 seconds   0.0.0.0:5000->5000/tcp, :::5000->5000/tcp   hungry_poitras
```

- Bilgisayarında browser aç ve http://localhost:5000/ adresine git.

### Oluşturulan İmajı Docker Hub’a Yükleme:


Docker Hub’a imaj yüklemek için öncelikle giriş yapmanız gerekir.

#### Adımlar:

1. Docker Hub’a giriş yapın:
```bash
docker login
```
Output
```
Login Succeeded
```

- Image tagleme:

```bash
docker tag my-flask-app your_dockerhub_username/my-flask-app:1.0
```

- Image gönderme:

```bash
docker push your_dockerhub_username/my-flask-app:1.0
```

## VOLUME OLUŞTURMA

Docker'da volume, konteynerler arasında veri paylaşımı sağlamak veya verilerin konteyner silinse bile kalıcı olmasını sağlamak için oluşturulur. Docker volume'ları, host işletim sistemindeki Docker'ın yönetimindeki bir alanda saklanır. Bu genellikle Linux'ta şu dizindir: /var/lib/docker/volumes/

Burada her volume, kendi özel klasöründe saklanır ve konteynerler bu dizine bağlanarak veriye erişir.

#### Volumsüz çalışma denemesi

- Volumeleri listele

```bash
docker volume ls
```

- postgresql volumsüz çalıştırma 

```bash
docker run -d --name postgres_no_volume -e POSTGRES_PASSWORD=password -p 5432:5432 postgres:latest
```

- Konteynere bağlan ve tablo oluştur. 

```bash
docker exec -it postgres_no_volume psql -U postgres
```
```psql
CREATE DATABASE my_database;

\c my_database

CREATE TABLE my_table (id SERIAL PRIMARY KEY, name VARCHAR(255));

INSERT INTO my_table (name) VALUES ('Row 1'), ('Row 2'), ('Row 3');

\dt

select * from my_table;
```

- Çıkmak için kısayol **CTRL+D**

- PostgreSQL konteynerini durdur ve kaldır. 

```bash
docker stop postgres_no_volume
```

```bash
docker rm postgres_no_volume
```
- Şimdi tekrar başlatalım
```bash
docker run -d --name postgres_no_volume -e POSTGRES_PASSWORD=password -p 5432:5432 postgres:latest
```

- Veritabanına bağlanın ve kontrol edin (verilerin kalıcı olmadığını görmek yeterlidir)

```bash
docker exec -it postgres_no_volume psql -U postgres
```

```pqsl
\c my_database
```
Failed verecek...

Konteyneri durdur ve sil. 

### VOLUME İle Çalıştırma

- Postgresql konteynerinizi volume ile çalıştırın

```bash
docker run -d --name postgres_with_volume -e POSTGRES_PASSWORD=password -p 5432:5432 -v postgres_data:/var/lib/postgresql/data postgres:latest
```

- Postgresqle bağlanın ve veritabanını ve tabloyu tekrar oluşturun

```bash
docker exec -it postgres_with_volume psql -U postgres
```

```psql
CREATE DATABASE my_database;
\c my_database
CREATE TABLE my_table (id SERIAL PRIMARY KEY, name VARCHAR(255));
INSERT INTO my_table (name) VALUES ('Row 1'), ('Row 2'), ('Row 3');
```

- Durdurun ve konteyneri kaldır

- Konteyneri tekrar çalıştır

```bash
docker run -d --name postgres_with_volume -e POSTGRES_PASSWORD=password -p 5432:5432 -v postgres_data:/var/lib/postgresql/data postgres:latest
```

- Connect to postgresql 
```bash
docker exec -it postgres_with_volume psql -U postgres
```

```psql
\c my_database
```

```pqsl
select * from my_table;
```

- Konteynerleri durdurun ve kaldırın

- Aşağıdaki komutla volume kaldırabilirsiniz

```bash
docker volume rm <volume_name>
```
docker volume rm postgres_data

## AĞINIZI OLUŞTURUN
Docker’da ağ (network) oluşturmanın amacı, konteynerler arasındaki iletişimi sağlamak veya konteynerleri dış dünyaya (örneğin, internet ya da diğer makineler) bağlamaktır. Ayrıca, izole ağlar kullanarak güvenlik ve kontrol artırılır. 

- Docker ağlarını listeleyin

```bash
docker network ls
```

- Yeni network oluştur.

```bash
docker network create my_nw_1
```

### FARKLI AĞ KULLANARAK KONTAYNERLERİ ÇALIŞTIRIN

- Uygulama ve postgres konteynerlerini yeni farklı ağ ile çalıştırın

```bash
docker run -d --name postgres -e POSTGRES_PASSWORD=password -p 5432:5432 --network my_nw_1 postgres:latest
```

- app konteynerine bağlan

docker run --name app --network my_nw_1 -p 5050:5000 -d my-flask-app

```bash
docker exec -it app bash
```

- Uygulama kapsayıcısında apt-get paket yöneticisini güncelleyin ve "ping"i yükleyin

```bash
apt-get update
```
```bash
apt-get install iputils-ping -y
```

- Postgres konteynerine "ping" atmayı deneyelim. Konteynerler kendi adlarını kullanarak birbirleriyle iletişim kurabilirler

```bash
ping postgres
```
```
root@e18ca5747556:/app# ping postgres
ping: postgres: Name or service not known
```

- Konteyneri durdurun ve silin

### AYNI AĞDA KONTAYNERLERİ ÇALIŞTIRIN

- Uygulama ve postgres konteynerlerini aynı ağda çalıştırın

```bash
docker run -d --name postgres -e POSTGRES_PASSWORD=password -p 5432:5432 --network my_nw_1 postgres:latest
```

- Uygulama konteynerini bağlayın ve ping komutunu tekrar yükleyin (yukarıdaki aynı kod)

```bash
ping postgres
```

- Konteyneri durdurun ve silin

- Networkünüzü silebilirsiniz

```bash
docker network rm <network_name>
```

## BİRÇOK KONTEYNER ÇALIŞTIRIN VE YÖNETİN

- Yeni dosya oluştur 

cd ..

```bash
mkdir my-first-compose
```

- Dizine git

```bash
cd my-first-compose
```

- Yeni docker-compose dosyası oluştur

```bash
touch docker-compose.yaml
```

- docker-compose.yml içine aşağıdaki içeriği kopyalayıp yapıştırın

```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine 
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html # bind mount volume (dependent on the directory structure and OS of the host)
    networks:
      - webnet

  db:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: sampledb
      MYSQL_USER: sampleuser
      MYSQL_PASSWORD: samplepassword
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql # named volumes (managed by docker and stored within docker environment)
    networks:
      - webnet

networks:
  webnet:

volumes:
  db_data:
```
for arm 
```
# version özelliği kaldırıldı (obsolete)

services:
  web:
    image: nginx:alpine 
    platform: linux/amd64  # Platform belirtme
    ports:
      - "8080:80"
    volumes:
      - html:/usr/share/nginx/html
    networks:
      - webnet

  db:
    image: mysql:5.7
    platform: linux/amd64  # Platform belirtme
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: sampledb
      MYSQL_USER: sampleuser
      MYSQL_PASSWORD: samplepassword
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - webnet

networks:
  webnet:

volumes:
  db_data:
  html:  # Bu satırı ekleyin
```

- Yeni html dosyası oluştur

```bash
mkdir html
```

```bash
cd html
```

```bash
touch index.html
```
- Aşağıdaki içeriği kopyalayıp index.html'ye yapıştırın

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My Dockerized Web App</title>
</head>
<body>
    <h1>Hello from Docker!</h1>
    <p>This is a simple web page served by Nginx inside a Docker container.</p>
</body>
</html>
```
- Tekrar my-first-compose dizinine gidin


```bash
cd ..
```

- Tree komutunu kullanarak dizin yapısını denetle

```bash
tree .
```
```
(base) [train@localhost my-first-compose]$ tree .
.
├── docker-compose.yaml
└── html
    └── index.html

1 directory, 2 files
```

- Konteyner oluşturun ve başlatın

```bash
docker compose up --build -d
```
- Bilgisayarınızda bir tarayıcı açın ve şuraya gidin: http://localhost:8080/

- Konteynerleri durdurmak için
```bash
docker compose down
```

docker exec -it my-first-compose-web-1 sh
docker exec -it my-first-compose-db-1 mysql -u sampleuser -p sampledb


## Loglar, inceleme ve temizleme

- Konteyner hakkında detaylı bilgi almak için

```bash
docker inspect <container_id_or_name>
```

- Logları görmek için
```bash
docker logs <container_id_or_name>
```

- Tüm imajları, konteynerleri, ağları (isteğe bağlı volumleri) temizlemek için
```bash
docker system prune -a
```

- Varsayılan olarak, şu anda birimi kullanan bir kapsayıcı yoksa önemli verilerin silinmesini önlemek için birimler kaldırılmaz. Anonim birimleri de budamak için komutu çalıştırırken --volumes işaretini kullanın

```bash
docker system prune -a --volumes
```