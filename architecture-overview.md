# SubastApp - Arquitectura del Sistema

## 🏗️ Visión General de la Arquitectura

SubastApp es una aplicación de subastas en tiempo real que utiliza una arquitectura de microservicios containerizada con comunicación en tiempo real mediante WebSockets.

## 📊 Diagrama de Arquitectura

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Cliente Web   │    │   Cliente Web   │    │   Cliente Web   │
│   (React App)   │    │   (React App)   │    │   (React App)   │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 │
                    ┌─────────────▼─────────────┐
                    │        Nginx (80/443)     │
                    │   (Proxy + Static Files)  │
                    └─────────────┬─────────────┘
                                  │
                    ┌─────────────▼─────────────┐
                    │      Backend (3001)       │
                    │  (Node.js + Socket.IO)    │
                    └─────────────┬─────────────┘
                                  │
                    ┌─────────────▼─────────────┐
                    │      MySQL (3306)         │
                    │    (Base de Datos)        │
                    └───────────────────────────┘
```

## 🐳 Arquitectura de Contenedores

### Docker Compose Services

```yaml
services:
  mysql:
    image: mysql:8.0
    container_name: subasta_app-mysql
    environment:
      - MYSQL_ROOT_PASSWORD=subasta_app_root_password
      - MYSQL_DATABASE=subasta_app
      - MYSQL_USER=subasta_app_user
      - MYSQL_PASSWORD=subasta_app_password
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
      - ./Dump20250821.sql:/docker-entrypoint-initdb.d/init.sql

  backend:
    build: ./backend
    container_name: subastapp-backend
    environment:
      - DATABASE_URL=mysql://subasta_app_user:subasta_app_password@mysql:3306/subasta_app
      - NODE_ENV=production
      - PORT=3001
    ports:
      - "3001:3001"
    depends_on:
      - mysql

  nginx:
    image: nginx:alpine
    container_name: subastapp-nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./frontend/dist:/usr/share/nginx/html
    depends_on:
      - backend
```

## 🔧 Componentes del Sistema

### 1. Frontend (React + TypeScript)

**Tecnologías:**
- React 18.2.0
- TypeScript 5.0.2
- Vite 4.4.5 (build tool)
- Tailwind CSS 3.3.3
- Zustand 4.4.1 (state management)
- Socket.IO Client 4.7.2

**Características:**
- Single Page Application (SPA)
- Responsive design con Tailwind CSS
- State management con Zustand
- Comunicación en tiempo real con Socket.IO
- Build optimizado con Vite

**Estructura de archivos:**
```
frontend/
├── src/
│   ├── components/
│   ├── context/
│   ├── hooks/
│   ├── pages/
│   ├── services/
│   └── assets/
├── public/
├── package.json
├── vite.config.ts
└── tailwind.config.js
```

### 2. Backend (Node.js + TypeScript)

**Tecnologías:**
- Node.js 18
- TypeScript 5.9.2
- Express 5.1.0
- Socket.IO 4.8.1
- Prisma 6.14.0 (ORM)
- MySQL2 3.14.3

**Características:**
- API RESTful con Express
- WebSockets con Socket.IO
- ORM con Prisma
- Autenticación simple por email
- Sistema de turnos para subastas

**Estructura de archivos:**
```
backend/
├── src/
│   ├── controllers/
│   ├── routes/
│   ├── services/
│   ├── sockets/
│   └── scripts/
├── prisma/
│   └── schema.prisma
├── package.json
└── Dockerfile
```

### 3. Base de Datos (MySQL)

**Esquema de base de datos:**
```sql
-- Tablas principales
Usuario (id, email, nombre, creditos, orden, salioDePuja, createdAt, updatedAt)
Item (id, rapidapiId, nombre, pais, equipo, posicion, subastado, precioInicial, imagen, ganadorId, createdAt, updatedAt)
Puja (id, monto, itemId, usuarioId, createdAt)
Configuracion (id, clave, valor, descripcion)
EstadoSubasta (id, subastaActiva, turnoActual, tiempoRestante, createdAt, updatedAt)
```

**Características:**
- MySQL 8.0
- InnoDB engine
- UTF-8 encoding
- Índices optimizados
- Relaciones con foreign keys

### 4. Proxy/Web Server (Nginx)

**Configuración:**
```nginx
events {
    worker_connections 1024;
}

http {
    server {
        listen 80;
        
        # Servir archivos estáticos del frontend
        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html;
        }
        
        # Proxy para API del backend
        location /api {
            proxy_pass http://backend:3001;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
        
        # Proxy para WebSockets
        location /socket.io {
            proxy_pass http://backend:3001;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    }
}
```

## 🔄 Flujo de Datos

### 1. Autenticación
```
Cliente → Nginx → Backend → MySQL
   ↑                                    ↓
   └────────── Respuesta JSON ──────────┘
```

### 2. Subasta en Tiempo Real
```
Cliente A → Socket.IO → Backend → MySQL
   ↑                                    ↓
   └────────── Broadcast ───────────────┘
                    ↓
                Cliente B, C, D...
```

### 3. Pujas
```
Cliente → HTTP POST → Backend → MySQL → Broadcast → Todos los clientes
```

## 🔒 Seguridad

### Configuraciones de Seguridad Implementadas

1. **Docker Security:**
   - Contenedores ejecutándose como usuario no-root
   - Red aislada entre contenedores
   - Volúmenes persistentes para datos

2. **Network Security:**
   - Solo puertos necesarios expuestos (80, 443, 3001, 3306)
   - Comunicación interna entre contenedores
   - Proxy reverso para ocultar backend

3. **Application Security:**
   - CORS configurado para IPs específicas
   - Validación de entrada en API
   - Autenticación por email (simple pero funcional)

4. **Data Security:**
   - Credenciales de base de datos específicas
   - Variables de entorno para configuración
   - Backup de datos con dump SQL

## 📈 Escalabilidad

### Estrategias de Escalabilidad

1. **Horizontal Scaling:**
   - Backend puede escalar horizontalmente
   - Load balancer puede distribuir carga
   - Base de datos puede replicarse

2. **Vertical Scaling:**
   - Droplet puede aumentar recursos
   - Contenedores pueden usar más memoria/CPU
   - Base de datos puede optimizarse

3. **Caching:**
   - Redis puede agregarse para cache
   - Nginx puede cachear archivos estáticos
   - CDN puede servir assets

## 🔍 Monitoreo y Logs

### Logs Disponibles

1. **Application Logs:**
   - Logs de Node.js en contenedor backend
   - Logs de React en build process
   - Logs de Prisma para queries

2. **Infrastructure Logs:**
   - Logs de Docker Compose
   - Logs de Nginx
   - Logs de MySQL

3. **System Logs:**
   - Logs del sistema operativo
   - Logs de Docker daemon
   - Logs de red

## 🚀 Deployment

### Infraestructura como Código

1. **Terraform:**
   - Definición de recursos Digital Ocean
   - Configuración de red y seguridad
   - Variables de entorno

2. **GitHub Actions:**
   - CI/CD pipeline
   - Despliegue automático
   - Testing y validación

3. **Docker:**
   - Imágenes optimizadas
   - Multi-stage builds
   - Volúmenes persistentes

## 📊 Métricas y KPIs

### Métricas de Rendimiento

1. **Application Metrics:**
   - Tiempo de respuesta de API
   - Número de conexiones WebSocket
   - Tasa de errores

2. **Infrastructure Metrics:**
   - Uso de CPU y memoria
   - Espacio en disco
   - Ancho de banda

3. **Business Metrics:**
   - Número de usuarios activos
   - Número de pujas por minuto
   - Tiempo promedio de subasta

---

**Nota**: Esta arquitectura está diseñada para ser escalable, mantenible y segura, permitiendo futuras mejoras y optimizaciones según las necesidades del negocio.
