# `test-simple-stock-flow-infra`

Repositorio de infraestructura, orquestación y despliegue local mediante Docker Compose para el proyecto formativo **Simple Stock Flow**.

## Requisitos
- Docker Desktop (con WSL2 habilitado en Windows)
- Git

## Guía de Puesta en Marcha

1. **Clonar los tres repositorios hermanos en la misma carpeta raíz:**
   ```bash
   workspace/
   ├── test-simple-stock-flow-infra/
   ├── test-simple-stock-flow-api/
   └── test-simple-stock-flow-app/
   ```

2. **Copiar las variables de entorno:**
   ```bash
   cp .env.example .env
   cp ../test-simple-stock-flow-api/.env.example ../test-simple-stock-flow-api/.env
   cp ../test-simple-stock-flow-app/.env.example ../test-simple-stock-flow-app/.env
   ```

3. **Levantar los servicios:**
   ```bash
   docker compose up -d --build
   ```

4. **Ejecutar migraciones de base de datos:**
   ```bash
   docker compose run --rm api php artisan migrate --seed
   ```

5. **Ejecutar pruebas y verificación automatizada:**
   ```bash
   docker compose run --rm api bash verify.sh
   ```

## Servicios y Puertos
- **Frontend (App):** [http://localhost:3000](http://localhost:3000)
- **Backend (API):** [http://localhost:8000/api](http://localhost:8000/api)
- **MySQL 8.4:** `localhost:3306`
