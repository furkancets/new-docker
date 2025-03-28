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
