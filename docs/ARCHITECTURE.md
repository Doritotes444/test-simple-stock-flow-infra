# Arquitectura de Infraestructura y Despliegue Docker

## 1. Topología de Servicios
Este repositorio orquesta los tres componentes del sistema **Simple Stock Flow** mediante Docker Compose:

```
┌─────────────────────────────────────────────────────────────┐
│                 test-simple-stock-flow-infra                │
│                                                             │
│   ┌────────────────┐   ┌────────────────┐   ┌───────────┐   │
│   │   MySQL 8.4    │   │  API (Laravel) │   │APP (React)│   │
│   │   Puerto: 3306 │◄──┤  Puerto: 8000  │◄──┤Puerto:3000│   │
│   │   (Motor Vacío)│   │  (PHP 8.2 FPM) │   │  (Nginx)  │   │
│   └────────────────┘   └────────────────┘   └───────────┘   │
│           ▲                     ▲                 ▲         │
│           │                     │                 │         │
│     [db_data vol]        [api_vendor vol]  [node_modules]   │
└─────────────────────────────────────────────────────────────┘
```

## 2. Decisiones de Arquitectura (ADR)
- **ADR-001 (Motor MySQL Vacío)**: El contenedor de base de datos arranca sin scripts DDL manuales (`.sql`). El esquema y tablas son creados y mantenidos exclusivamente por las migraciones de Laravel (`php artisan migrate`).
- **ADR-002 (Volúmenes Aislados en Windows)**: Las carpetas pesadas `vendor/` de PHP y `node_modules/` de Node se almacenan en volúmenes nombrados de Docker (`api_vendor` y `app_node_modules`) para evitar problemas de permisos y bloqueos de I/O de Windows/WSL2.
- **ADR-003 (Repositorios Hermanos)**: Los tres repositorios (`infra`, `api` y `app`) residen en la misma raíz para permitir referencias relativas en Docker Compose (`../test-simple-stock-flow-api` y `../test-simple-stock-flow-app`).

## 3. Puertos Expuestos
- **App (Frontend):** `http://localhost:3000`
- **API (Backend):** `http://localhost:8000`
- **DB (MySQL 8.4):** `localhost:3306`

## 4. Flujo de Inicialización
1. Copiar `.env.example` a `.env` en los tres repositorios.
2. Levantar los contenedores: `docker compose up -d --build`.
3. Ejecutar las migraciones desde el contenedor de API:
   ```bash
   docker compose run --rm api php artisan migrate --seed
   ```
4. Ejecutar el script de verificación automatizada:
   ```bash
   docker compose run --rm api bash verify.sh
   ```
