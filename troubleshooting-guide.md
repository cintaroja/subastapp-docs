# SubastApp - Guía de Solución de Problemas

## 🔍 Problemas Comunes y Soluciones

Esta guía documenta los problemas encontrados durante el despliegue y sus soluciones para futuras referencias.

## 🚨 Problemas Críticos

### 1. Error: "Failed to fetch" en Frontend

**Síntomas:**
- Frontend no puede conectar con backend
- Error en consola del navegador
- Página de login no funciona

**Causas posibles:**
1. CORS mal configurado
2. Backend no está funcionando
3. Proxy de Nginx mal configurado
4. Variables de entorno incorrectas

**Soluciones:**

#### A. Verificar CORS
```bash
# Verificar configuración de CORS en backend
ssh root@159.223.6.121 "docker logs subastapp-backend | grep CORS"

# Verificar que la IP de producción esté en la configuración
ssh root@159.223.6.121 "grep -r '159.223.6.121' /home/subastapp/SubastApp/backend/src/"
```

#### B. Verificar Backend
```bash
# Verificar si el backend está funcionando
ssh root@159.223.6.121 "curl -s http://localhost:3001/"

# Verificar logs del backend
ssh root@159.223.6.121 "docker logs subastapp-backend --tail 20"
```

#### C. Verificar Nginx
```bash
# Verificar configuración de Nginx
ssh root@159.223.6.121 "nginx -t"

# Verificar logs de Nginx
ssh root@159.223.6.121 "docker logs subasta_app-nginx --tail 10"
```

### 2. Error: "Can't reach database server"

**Síntomas:**
- Backend no puede conectar a MySQL
- Errores de Prisma en logs
- API no responde

**Causas posibles:**
1. MySQL no está funcionando
2. Credenciales incorrectas
3. Red Docker mal configurada
4. Variables de entorno incorrectas

**Soluciones:**

#### A. Verificar MySQL
```bash
# Verificar si MySQL está funcionando
ssh root@159.223.6.121 "docker ps | grep mysql"

# Verificar logs de MySQL
ssh root@159.223.6.121 "docker logs subasta_app-mysql --tail 10"

# Probar conexión a MySQL
ssh root@159.223.6.121 "docker exec subasta_app-mysql mysql -u subasta_app_user -psubasta_app_password -e 'SELECT 1;'"
```

#### B. Verificar Variables de Entorno
```bash
# Verificar DATABASE_URL en el backend
ssh root@159.223.6.121 "docker exec subastapp-backend env | grep DATABASE"
```

#### C. Verificar Docker Compose
```bash
# Verificar configuración de Docker Compose
ssh root@159.223.6.121 "cd /home/subastapp/SubastApp && docker-compose config"

# Reiniciar servicios
ssh root@159.223.6.121 "cd /home/subastapp/SubastApp && docker-compose restart"
```

### 3. Error: "npm ci" JSON Parse Error

**Síntomas:**
- Build de Docker falla
- Error "Unexpected token \u0000"
- Archivos package.json corruptos

**Causa:**
- Archivos transferidos desde Windows a Linux
- Caracteres null en archivos JSON

**Solución:**
```bash
# Recrear package.json manualmente
# Ver contenido del archivo
ssh root@159.223.6.121 "file /home/subastapp/SubastApp/frontend/package.json"

# Recrear archivo localmente y transferir
# Usar sftp en lugar de scp para transferencia más confiable
sftp root@159.223.6.121 << EOF
cd /home/subastapp/SubastApp/frontend
put frontend/package.json
bye
EOF
```

### 4. Error: "EBADENGINE" npm

**Síntomas:**
- Error de versión de Node.js
- Conflictos de peer dependencies
- Build falla por incompatibilidades

**Solución:**
```bash
# Modificar Dockerfile para usar --legacy-peer-deps
# En Dockerfile del frontend:
RUN npm install --legacy-peer-deps

# O downgrade dependencias en package.json
# Cambiar versiones a compatibles con Node.js 18
```

### 5. Error: "tar: file changed as we read it"

**Síntomas:**
- GitHub Actions falla durante transferencia
- Error durante despliegue automático
- Archivos temporales causan problemas

**Solución:**
```bash
# Cambiar de tar a rsync en GitHub Actions
# En .github/workflows/deploy-app.yml:
rsync -avz --exclude='.git' --exclude='node_modules' --exclude='dist' -e ssh ./ root@$DROPLET_IP:/home/subastapp/SubastApp/
```

## 🔧 Problemas de Configuración

### 1. Problemas de CORS

**Síntomas:**
- Frontend no puede hacer requests al backend
- Errores de CORS en consola del navegador

**Solución:**
```javascript
// En backend/src/server.ts
app.use(cors({
  origin: [
    "http://localhost:5173", 
    "http://192.168.18.124:5173", 
    "http://127.0.0.1:5173", 
    "http://159.223.6.121", 
    "http://159.223.6.121:80"
  ],
  credentials: true
}));
```

### 2. Problemas de Base de Datos

**Síntomas:**
- Tablas no existen
- Errores de case sensitivity
- Datos no se insertan correctamente

**Solución:**
```bash
# Verificar tablas existentes
ssh root@159.223.6.121 "docker exec subasta_app-mysql mysql -u subasta_app_user -psubasta_app_password subasta_app -e 'SHOW TABLES;'"

# Verificar datos
ssh root@159.223.6.121 "docker exec subasta_app-mysql mysql -u subasta_app_user -psubasta_app_password subasta_app -e 'SELECT COUNT(*) FROM Usuario;'"

# Recrear tablas si es necesario
ssh root@159.223.6.121 "docker exec subastapp-backend npx prisma db push"
```

### 3. Problemas de Nginx

**Síntomas:**
- Error 403 Forbidden
- Proxy no funciona
- Archivos estáticos no se sirven

**Solución:**
```bash
# Verificar configuración de Nginx
ssh root@159.223.6.121 "cat /home/subastapp/SubastApp/nginx/nginx.conf"

# Verificar que los archivos del frontend existan
ssh root@159.223.6.121 "ls -la /home/subastapp/SubastApp/frontend/dist/"

# Reiniciar Nginx
ssh root@159.223.6.121 "docker restart subasta_app-nginx"
```

## 🐳 Problemas de Docker

### 1. Contenedores no se inician

**Comandos de diagnóstico:**
```bash
# Verificar estado de contenedores
ssh root@159.223.6.121 "docker ps -a"

# Ver logs de contenedores
ssh root@159.223.6.121 "docker logs subastapp-backend"

# Verificar recursos del sistema
ssh root@159.223.6.121 "docker system df"

# Limpiar recursos no utilizados
ssh root@159.223.6.121 "docker system prune -f"
```

### 2. Problemas de red Docker

**Comandos de diagnóstico:**
```bash
# Verificar redes Docker
ssh root@159.223.6.121 "docker network ls"

# Verificar conectividad entre contenedores
ssh root@159.223.6.121 "docker exec subastapp-backend ping mysql"

# Verificar configuración de red
ssh root@159.223.6.121 "docker network inspect subastapp_subasta_app-network"
```

### 3. Problemas de volúmenes

**Comandos de diagnóstico:**
```bash
# Verificar volúmenes
ssh root@159.223.6.121 "docker volume ls"

# Verificar contenido de volúmenes
ssh root@159.223.6.121 "docker run --rm -v subastapp_mysql_data:/data alpine ls -la /data"
```

## 🔐 Problemas de Seguridad

### 1. Información sensible expuesta

**Síntomas:**
- API keys en repositorios
- Credenciales en archivos de configuración
- Claves SSH en repositorios

**Solución:**
```bash
# Eliminar archivos sensibles
rm -f .env backend/.env github_actions_key*

# Actualizar .gitignore
echo "*.env" >> .gitignore
echo "*_key*" >> .gitignore

# Regenerar credenciales comprometidas
# Actualizar variables de entorno en el servidor
```

### 2. Problemas de SSH

**Síntomas:**
- GitHub Actions no puede conectar
- Error "Permission denied (publickey)"

**Solución:**
```bash
# Regenerar SSH keys
ssh-keygen -t ed25519 -f github_actions_key -N ""

# Agregar clave pública al servidor
ssh root@159.223.6.121 "echo '$(cat github_actions_key.pub)' >> ~/.ssh/authorized_keys"

# Actualizar secret en GitHub
# Agregar nueva clave privada como DROPLET_SSH_KEY
```

## 📊 Comandos de Monitoreo

### Verificar Estado General
```bash
# Estado de todos los servicios
ssh root@159.223.6.121 "docker ps"

# Uso de recursos
ssh root@159.223.6.121 "htop"

# Espacio en disco
ssh root@159.223.6.121 "df -h"

# Memoria disponible
ssh root@159.223.6.121 "free -h"
```

### Verificar Logs
```bash
# Logs del backend
ssh root@159.223.6.121 "docker logs subastapp-backend --tail 50"

# Logs de Nginx
ssh root@159.223.6.121 "docker logs subasta_app-nginx --tail 50"

# Logs de MySQL
ssh root@159.223.6.121 "docker logs subasta_app-mysql --tail 50"
```

### Verificar Conectividad
```bash
# Probar API
ssh root@159.223.6.121 "curl -s http://localhost:3001/"

# Probar frontend
ssh root@159.223.6.121 "curl -s http://localhost:80/ | head -5"

# Probar base de datos
ssh root@159.223.6.121 "docker exec subasta_app-mysql mysql -u subasta_app_user -psubasta_app_password -e 'SELECT COUNT(*) FROM Usuario;'"
```

## 🚀 Procedimientos de Recuperación

### Reinicio Completo del Sistema
```bash
# Parar todos los servicios
ssh root@159.223.6.121 "cd /home/subastapp/SubastApp && docker-compose down"

# Limpiar recursos
ssh root@159.223.6.121 "docker system prune -f"

# Reiniciar servicios
ssh root@159.223.6.121 "cd /home/subastapp/SubastApp && docker-compose up -d"

# Verificar estado
ssh root@159.223.6.121 "docker ps"
```

### Restaurar desde Backup
```bash
# Restaurar base de datos
ssh root@159.223.6.121 "docker exec -i subasta_app-mysql mysql -u subasta_app_user -psubasta_app_password subasta_app < /home/subastapp/SubastApp/Dump20250821.sql"

# Verificar restauración
ssh root@159.223.6.121 "docker exec subasta_app-mysql mysql -u subasta_app_user -psubasta_app_password -e 'SELECT COUNT(*) FROM Usuario;'"
```

### Reconstruir Imágenes
```bash
# Reconstruir backend
ssh root@159.223.6.121 "cd /home/subastapp/SubastApp && docker-compose build backend"

# Reconstruir frontend
ssh root@159.223.6.121 "cd /home/subastapp/SubastApp && docker-compose build frontend"

# Reiniciar servicios
ssh root@159.223.6.121 "cd /home/subastapp/SubastApp && docker-compose up -d"
```

## 📞 Contactos de Soporte

### Equipo Técnico
- **Ingeniero de Sistemas** - Responsable de infraestructura
- **Daniel Otón** - Desarrollador
- **Cursor AI** - Soporte técnico

### Proveedores
- **Digital Ocean** - Soporte de infraestructura
- **GitHub** - Soporte de repositorios y Actions
- **Docker** - Soporte de contenedores

---

**Nota**: Esta guía debe actualizarse regularmente con nuevos problemas y soluciones encontradas durante el mantenimiento del sistema.
