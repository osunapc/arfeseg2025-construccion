# 🐳 Docker Deployment Guide

Este documento explica cómo desplegar la aplicación usando Docker.

## 📋 Prerequisitos

- Docker instalado (versión 20.10 o superior)
- Docker Compose instalado (opcional, pero recomendado)

## 🚀 Despliegue Rápido

### Opción 1: Usando Docker Compose (Recomendado)

```bash
# Construir y ejecutar el contenedor
docker-compose up -d

# Ver logs
docker-compose logs -f

# Detener el contenedor
docker-compose down
```

### Opción 2: Usando Docker directamente

```bash
# Construir la imagen
docker build -t construccion-app .

# Ejecutar el contenedor
docker run -d -p 80:80 --name construccion-web construccion-app

# Ver logs
docker logs -f construccion-web

# Detener el contenedor
docker stop construccion-web

# Eliminar el contenedor
docker rm construccion-web
```

## 🌐 Acceder a la Aplicación

Una vez que el contenedor esté ejecutándose, abre tu navegador y visita:

```
http://localhost
```

## 🔧 Configuración Avanzada

### Cambiar el Puerto

Para usar un puerto diferente (por ejemplo, 8080):

**Con Docker Compose:**

```yaml
# Edita docker-compose.yml
ports:
  - '8080:80'
```

**Con Docker:**

```bash
docker run -d -p 8080:80 --name construccion-web construccion-app
```

### Variables de Entorno

Puedes agregar variables de entorno en `docker-compose.yml`:

```yaml
environment:
  - NODE_ENV=production
  - API_URL=https://api.ejemplo.com
```

## 📦 Estructura de la Imagen

La imagen Docker usa una estrategia **multi-stage**:

1. **Stage 1 (Builder)**: Construye la aplicación Angular

   - Usa Node.js 20 Alpine
   - Instala dependencias
   - Compila el proyecto

2. **Stage 2 (Production)**: Sirve la aplicación
   - Usa Nginx Alpine (imagen ligera)
   - Solo contiene los archivos compilados
   - Configuración optimizada para producción

## 🔍 Comandos Útiles

```bash
# Ver contenedores en ejecución
docker ps

# Ver todas las imágenes
docker images

# Reconstruir la imagen (después de cambios)
docker-compose up -d --build

# Entrar al contenedor (debug)
docker exec -it construccion-web sh

# Ver uso de recursos
docker stats construccion-web

# Limpiar imágenes no utilizadas
docker image prune -a
```

## 🛡️ Características de Seguridad

El Nginx está configurado con:

- Headers de seguridad (X-Frame-Options, X-Content-Type-Options, X-XSS-Protection)
- Compresión Gzip para mejor rendimiento
- Cache de assets estáticos (1 año)
- Routing correcto para Angular SPA

## 📊 Optimizaciones

- **Imagen pequeña**: ~50MB (gracias a Alpine Linux)
- **Build rápido**: Cache de capas de Docker
- **Compresión Gzip**: Reduce el tamaño de transferencia
- **Cache de assets**: Mejora el rendimiento en clientes

## 🐛 Troubleshooting

### El contenedor no inicia

```bash
# Ver logs detallados
docker logs construccion-web

# Verificar que el puerto no esté en uso
netstat -ano | findstr :80  # Windows
lsof -i :80                 # Linux/Mac
```

### Cambios no se reflejan

```bash
# Reconstruir sin cache
docker-compose build --no-cache
docker-compose up -d
```

### Problemas de permisos

```bash
# En Linux, puede necesitar sudo
sudo docker-compose up -d
```

## 🚢 Despliegue en Producción

### Docker Hub

```bash
# Login
docker login

# Tag la imagen
docker tag construccion-app tu-usuario/construccion-app:latest

# Push a Docker Hub
docker push tu-usuario/construccion-app:latest
```

### Servidor Remoto

```bash
# En el servidor
docker pull tu-usuario/construccion-app:latest
docker run -d -p 80:80 --restart unless-stopped construccion-app
```

## 📝 Notas

- La aplicación se sirve en el puerto 80 por defecto
- Los assets estáticos se cachean por 1 año
- Nginx maneja el routing de Angular automáticamente
- La imagen final es muy ligera (~50MB)
