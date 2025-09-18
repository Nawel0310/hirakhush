# Docker Setup - Hirakhush

Este documento explica cómo levantar el proyecto completo usando Docker Compose.

## Estructura del Proyecto

```
hirakhush/
├── backend/           # Strapi CMS
├── frontend/          # Next.js App
├── nginx/             # Configuración Nginx
│   ├── nginx.conf
│   └── conf.d/
│       └── default.conf
├── docker-compose.yml # Configuración principal
└── .env              # Variables de entorno
```

## Servicios Incluidos

- **PostgreSQL**: Base de datos principal
- **Strapi Backend**: API y CMS (puerto interno 4000)
- **Next.js Frontend**: Aplicación web (puerto interno 3000)
- **Nginx**: Reverse proxy y servidor web (puerto 80/443)

## Configuración de Puertos

- **Puerto 80**: Aplicación completa (Frontend + API)
- **Frontend**: `http://localhost/`
- **Backend API**: `http://localhost/api/`
- **Admin Strapi**: `http://localhost/admin/`

## Comandos de Docker

### Levantar el proyecto completo
```bash
# Construir e iniciar todos los servicios
docker-compose up --build

# Ejecutar en background
docker-compose up -d --build
```

### Ver logs
```bash
# Todos los servicios
docker-compose logs -f

# Servicio específico
docker-compose logs -f frontend
docker-compose logs -f backend
docker-compose logs -f nginx
```

### Parar servicios
```bash
# Parar servicios
docker-compose down

# Parar y eliminar volúmenes (CUIDADO: elimina la base de datos)
docker-compose down -v
```

### Reconstruir servicios
```bash
# Reconstruir un servicio específico
docker-compose build frontend
docker-compose build backend

# Reconstruir todo
docker-compose build --no-cache
```

## Variables de Entorno

Asegúrate de tener configurado el archivo `.env` con las variables necesarias:

```env
# Frontend
NEXT_PUBLIC_STRAPI_URL=http://localhost/api

# Backend
HOST=0.0.0.0
PORT=4000
NODE_ENV=development

# Database
DATABASE_CLIENT=postgres
DATABASE_HOST=postgres
DATABASE_PORT=5432
DATABASE_NAME=hirakhush
DATABASE_USERNAME=hirakhush_user
DATABASE_PASSWORD=hirakhush_password

# Strapi secrets (generar nuevos para producción)
APP_KEYS=...
API_TOKEN_SALT=...
ADMIN_JWT_SECRET=...
TRANSFER_TOKEN_SALT=...
ENCRYPTION_KEY=...
JWT_SECRET=...
```

## Nginx Configuration

El reverse proxy de Nginx está configurado para:

- Servir el frontend de Next.js en `/`
- Proxy del backend Strapi en `/api/`
- Acceso al admin de Strapi en `/admin/`
- Servir archivos estáticos en `/uploads/`
- Rate limiting en API y admin
- Headers de seguridad
- Compresión gzip

## Desarrollo

Para desarrollo, puedes ejecutar los servicios individualmente:

```bash
# Solo base de datos
docker-compose up postgres

# Backend + database
docker-compose up postgres backend

# Todo excepto nginx (para desarrollo local)
docker-compose up postgres backend frontend
```

## Producción

Para producción:

1. Actualizar variables de entorno en `.env`
2. Configurar certificados SSL en `nginx/ssl/`
3. Descomentar la configuración HTTPS en `nginx/conf.d/default.conf`
4. Usar `NODE_ENV=production`

## Troubleshooting

### Puerto en uso
```bash
# Verificar qué proceso usa el puerto 80
sudo lsof -i :80

# Parar nginx del sistema si está corriendo
sudo systemctl stop nginx
```

### Problemas de conexión a la base de datos
```bash
# Verificar que postgres esté corriendo
docker-compose logs postgres

# Reiniciar solo la base de datos
docker-compose restart postgres
```

### Limpiar Docker
```bash
# Limpiar contenedores parados
docker container prune

# Limpiar imágenes no utilizadas
docker image prune

# Limpiar todo (CUIDADO)
docker system prune -a
```

## URLs de Acceso

Una vez levantado el proyecto:

- **Frontend**: http://localhost
- **Backend API**: http://localhost/api
- **Admin Strapi**: http://localhost/admin
- **Health Check**: http://localhost/health