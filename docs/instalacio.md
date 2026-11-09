# Instalacio de l'aplicacio

## 1. Requisits tecnics

| Categoría | Requisito | Detalle |
| --- | --- | --- |
| **Plataformas** | App móvil multiplataforma | Android 9+ e iOS 14+, desarrollada con Flutter o Kotlin Multiplatform |
| **Plataformas** | Panel web de administración | Aplicación web responsive para gestionar flota, estaciones y usuarios |
| **Backend** | API REST | Java con Spring Boot, documentada con OpenAPI/Swagger |
| **Backend** | Arquitectura | Microservicios (usuarios, viajes, pagos, flota, puntos verdes) en contenedores Docker |
| **Base de datos** | Datos relacionales | PostgreSQL con extensión PostGIS para datos geográficos |
| **Base de datos** | Caché y tiempo real | Redis para disponibilidad de bicis y sesiones |
| **Geolocalización** | Mapas y rutas | Integración con OpenStreetMap y motor de rutas para carril bici |
| **Geolocalización** | Precisión GPS | Posición de la bici actualizada cada 5 segundos durante el viaje |
| **IoT** | Candado inteligente | Comunicación Bluetooth LE y NFC con cada bicicleta |
| **IoT** | Telemetría | Módulo 4G/NB-IoT en cada bici (ubicación, batería, estado del candado) |
| **Desbloqueo** | Lectura de QR | Escaneo con cámara en menos de 2 segundos |
| **Pagos** | Pasarela de pago | Integración con Stripe o Redsys, cumplimiento PCI DSS |
| **Seguridad** | Autenticación | OAuth 2.0 + JWT, verificación en dos pasos opcional |
| **Seguridad** | Cifrado | TLS 1.3 en tránsito y AES-256 en reposo |
| **Privacidad** | Cumplimiento legal | RGPD, con consentimiento explícito para la ubicación y borrado de datos bajo petición |
| **Sostenibilidad** | Cálculo de CO₂ | Algoritmo basado en distancia y factores de emisión del transporte sustituido |
| **Sostenibilidad** | Calidad del aire | Consumo de datos abiertos de estaciones de medición en tiempo real |
| **Rendimiento** | Tiempo de respuesta | Menos de 500 ms en el 95 % de las peticiones |
| **Rendimiento** | Escalabilidad | Soporte de 50.000 usuarios concurrentes y 10.000 bicis conectadas |
| **Disponibilidad** | Uptime | 99,9 % mensual, con despliegue en la nube y balanceo de carga |
| **Notificaciones** | Push | Firebase Cloud Messaging para alertas de reserva, estaciones y retos |
| **Accesibilidad** | Estándares | WCAG 2.1 nivel AA, compatibilidad con lectores de pantalla |
| **Idiomas** | Localización | Valenciano, castellano e inglés |
| **Offline** | Modo sin conexión | Mapa en caché y cola de sincronización para viajes con mala cobertura |
| **DevOps** | Integración continua | Pipeline CI/CD con GitHub Actions, pruebas automáticas y despliegue en contenedores |
| **Monitorización** | Observabilidad | Prometheus y Grafana para métricas, y registro centralizado de errores |
| **Almacenamiento** | Copias de seguridad | Backup diario con retención de 30 días |

## 2. Passos per preparar l'entorn

### 1. Preparar l'equip

- Instal·lar un sistema operatiu actualitzat (per a iOS fa falta macOS amb Xcode)
- Instal·lar Git i configurar el nom d'usuari i el correu
- Triar un IDE: Android Studio per a l'app mòbil i IntelliJ IDEA per al backend

### 2. Instal·lar les eines del backend

- JDK 17 o superior
- Maven o Gradle
- Docker i Docker Compose

### 3. Instal·lar les eines de l'app mòbil

- Flutter SDK (o Kotlin Multiplatform, segons la decisió de l'equip)
- Android SDK i emuladors, i el simulador d'iOS si es treballa en Mac
- Verificar la instal·lació amb `flutter doctor`

### 4. Clonar el repositori i organitzar el projecte

- Clonar el repositori des de GitHub
- Crear les branques de treball (per exemple, `main`, `develop` i `feature/...`)
- Revisar l'estructura: carpetes per a l'app, el backend, la documentació i l'infraestructura

### 5. Aixecar els serveis amb Docker Compose

- PostgreSQL amb l'extensió PostGIS
- Redis per a la memòria cau
- Altres serveis auxiliars si cal (per exemple, un servidor de proves per a pagaments)

### 6. Configurar variables d'entorn i secrets

- Crear un fitxer `.env` local a partir d'un `.env.example`
- Definir les claus d'API (mapes, pagaments, notificacions) amb valors de prova
- No pujar mai els secrets al repositori; afegir-los al `.gitignore`

### 7. Inicialitzar la base de dades

- Executar les migracions (Flyway o Liquibase)
- Carregar dades de prova: estacions, bicicletes i usuaris fictus

### 8. Executar i comprovar el sistema

- Arrancar el backend i provar l'API amb Swagger o Postman
- Executar l'app en un emulador o en un dispositiu físic
- Comprovar que app i backend es comuniquen correctament

### 9. Configurar la qualitat del codi

- Executar les proves automàtiques (unitàries i d'integració)
- Activar un formatador i un analitzador de codi (linters)
- Configurar *pre-commit hooks* per evitar errors abans de fer commit

### 10. Configurar la integració contínua

- Crear el *pipeline* amb GitHub Actions: compilar, provar i construir les imatges Docker
- Definir els entorns de desplegament (desenvolupament, proves i producció)

### 11. Documentar l'entorn

- Escriure un `README` amb els passos d'instal·lació i execució
- Anotar els problemes habituals i les solucions

## 3. Script

El següent script permet fer la preparacio de l'entorn del punt 2 al punt 6, no copieu i executeu directament, ja que tindreu que cambiar algunes coses:

```bash
#!/bin/sh
# Script de preparació de l'entorn de desenvolupament d'EcoRide
# Pensat per a Ubuntu/Debian (usa apt i snap). Executa'l amb: sh setup-entorn.sh
set -e

# ---------- CONFIGURACIÓ (modifica estos valors) ----------
REPO_URL="https://github.com/Simhernorn/EcoRide.git"
REPO_DIR="ecoride"
FEATURE_BRANCH="feature/nova-funcionalitat"

# ---------- 1. Instal·lació d'eines ----------
sudo apt-get update
sudo apt-get install -y openjdk-21-jdk
sudo apt-get install -y maven
sudo apt-get install -y gradle
sudo apt-get install -y docker.io
sudo apt-get install -y docker-compose-v2
sudo snap install flutter --classic
sudo apt-get install -y android-sdk

# Permet usar Docker sense sudo en futures sessions
sudo usermod -aG docker "$USER" || true

# ---------- 2. Clonar el repositori ----------
git clone "$REPO_URL" "$REPO_DIR"
cd "$REPO_DIR"

# ---------- 3. Crear les branques ----------
# Si el repositori està buit, crea un commit inicial perquè es puguen crear branques
if ! git rev-parse --verify HEAD >/dev/null 2>&1; then
    git commit --allow-empty -m "Commit inicial"
fi

git checkout -B main
git branch -f develop
git branch -f "$FEATURE_BRANCH"
git checkout develop

# ---------- 4. Aixecar PostgreSQL (PostGIS) i Redis amb Docker Compose ----------
if [ ! -f docker-compose.yml ]; then
cat > docker-compose.yml <<'EOF'
services:
  postgres:
    image: postgis/postgis:16-3.4
    container_name: ecoride-postgres
    environment:
      POSTGRES_DB: ${POSTGRES_DB:-ecoride}
      POSTGRES_USER: ${POSTGRES_USER:-ecoride}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD:-ecoride}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7
    container_name: ecoride-redis
    ports:
      - "6379:6379"

volumes:
  postgres_data:
EOF
fi

sudo docker compose up -d

# ---------- 5. Crear el fitxer .env amb claus d'API de prova ----------
if [ -f .env.example ]; then
    cp .env.example .env
else
    : > .env
fi

set_var POSTGRES_DB ecoride
set_var POSTGRES_USER ecoride
set_var POSTGRES_PASSWORD ecoride
set_var REDIS_HOST localhost
set_var REDIS_PORT 6379
set_var MAPS_API_KEY test_maps_api_key_123456
set_var PAYMENTS_API_KEY sk_test_payments_123456
set_var FCM_SERVER_KEY test_fcm_server_key_123456
set_var AIR_QUALITY_API_KEY test_air_quality_key_123456

# El .env no s'ha de pujar mai al repositori
grep -qx ".env" .gitignore 2>/dev/null || echo ".env" >> .gitignore

echo "Entorn preparat correctament en: $(pwd)"
```
