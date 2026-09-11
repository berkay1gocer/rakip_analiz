# Rakip Analiz SaaS

Google yorumlarını analiz ederek rakip işletmeleri karşılaştırmayı hedefleyen
SaaS projesi.

## Mevcut Durum

Projenin mevcut backend temeli Django ile oluşturulmuştur. Şu anda veritabanı
bağlantısı ve Django yönetim paneli hazırdır; rakip analizi, yorum toplama ve
embedding API'leri henüz geliştirme aşamasındadır.

## Teknolojiler

- Python 3
- Django 6.1.1
- PostgreSQL 17
- pgvector: embedding vektörlerini PostgreSQL içinde saklamak ve aramak için
- Psycopg 3: Django ile PostgreSQL bağlantısı için
- Docker Compose: PostgreSQL ortamı için

Planlanan bileşenler:

- Ollama ve Gemma: yerel yapay zeka modeli
- Redis: önbellek ve görev kuyruğu
- SerpAPI: arama sonuçları
- FastAPI: bağımsız yapay zeka servisi gerekirse

## Proje Yapısı

```text
config/                 Django ayarları ve URL yapılandırması
docker/postgres/        PostgreSQL başlangıç SQL dosyaları
backend/                Backend uygulamaları için klasör
ai-service/             Yapay zeka servisi için klasör
frontend/               Frontend uygulaması için klasör
tests/                  Testler
manage.py               Django komut satırı aracı
docker-compose.yml      pgvector destekli PostgreSQL servisi
```

## Kurulum

### 1. Sanal ortamı etkinleştir

Windows PowerShell:

```powershell
& .venv\Scripts\Activate.ps1
```

Sanal ortam henüz oluşturulmadıysa:

```powershell
python -m venv .venv
& .venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

### 2. PostgreSQL ve pgvector'ı başlat

Docker Desktop açıkken:

```powershell
docker compose up -d
```

Veritabanı ayarları geliştirme ortamı için `docker-compose.yml` içinde
tanımlıdır:

```text
Veritabanı: competitor_analysis
Kullanıcı:  admin
Port:      5432
```

`pgvector` extension'ı yeni bir PostgreSQL volume'ü oluşturulurken otomatik
olarak etkinleştirilir. Mevcut bir volume kullanılıyorsa bir kez şu komut
çalıştırılmalıdır:

```powershell
docker compose exec postgres psql -U admin -d competitor_analysis -c "CREATE EXTENSION IF NOT EXISTS vector;"
```

### 3. Django veritabanını hazırla

```powershell
python manage.py migrate
```

İsteğe bağlı olarak yönetici kullanıcısı oluşturulabilir:

```powershell
python manage.py createsuperuser
```

### 4. Geliştirme sunucusunu çalıştır

```powershell
python manage.py runserver
```

Uygulama: <http://127.0.0.1:8000/>

Yönetim paneli: <http://127.0.0.1:8000/admin/>

## Faydalı Komutlar

```powershell
# PostgreSQL servis durumunu kontrol et
docker compose ps

# PostgreSQL loglarını görüntüle
docker compose logs postgres

# Servisleri durdur
docker compose down
```

`docker compose down -v` PostgreSQL volume'ünü ve içindeki verileri siler.
Geliştirme verilerini korumak için bu komutu dikkatli kullanın.

## Notlar

- `docker-compose.yml` içindeki parola yalnızca yerel geliştirme içindir.
- Üretim ortamında gizli bilgiler environment variable veya secret yönetimi
	ile sağlanmalıdır.
- `backend/`, `ai-service/` ve `frontend/` klasörleri için uygulama kodu
	henüz eklenmektedir.