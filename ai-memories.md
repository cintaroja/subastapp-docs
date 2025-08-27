# SubastApp - Memories del AI Assistant

## 📋 Resumen de Memories Aprendidos

Este documento registra todos los "memories" (recuerdos) que el AI Assistant ha aprendido durante la interacción con el usuario, incluyendo preferencias, patrones de comportamiento y configuraciones específicas.

## 🎯 Memories Comunicados y Aceptados por el Usuario

### 1. **Preferencia de Transferencia de Archivos**
- **Memory ID**: 6996695
- **Descripción**: El usuario prefiere usar `sftp` para transferencias de archivos en lugar de `scp`
- **Contexto**: Durante el despliegue, el usuario solicitó explícitamente usar `sftp` para todas las transferencias
- **Impacto**: Todas las transferencias posteriores se realizaron con `sftp`
- **Estado**: ✅ **Aceptado y aplicado**

### 2. **Configuración de Git Init**
- **Memory ID**: 6989108
- **Descripción**: El usuario prefiere ejecutar `git init` solo en la carpeta del proyecto SUBASTAPP, no en el subdirectorio Code
- **Contexto**: Durante la configuración inicial de repositorios
- **Impacto**: Configuración de Git realizada en el nivel correcto del proyecto
- **Estado**: ✅ **Aceptado y aplicado**

### 3. **Separación de Infraestructura**
- **Memory ID**: 6989101
- **Descripción**: El usuario prefiere separar el código de infraestructura (Terraform) en un repositorio dedicado, separado del repositorio principal de la aplicación
- **Contexto**: Durante la planificación de la arquitectura del proyecto
- **Impacto**: Creación de repositorio `subastapp-infra` separado
- **Estado**: ✅ **Aceptado y aplicado**

### 4. **Actualización de IPs y Despliegue**
- **Memory ID**: 6998077
- **Descripción**: El usuario solicitó reemplazar todas las ocurrencias de IP local (192.168.18.124) por IP de producción (159.223.6.121) y desplegar
- **Contexto**: Actualización de configuración para producción
- **Impacto**: 
  - Reemplazo de IPs en archivos de configuración
  - Despliegue exitoso en servidor remoto
  - Corrección de configuración Nginx
- **Estado**: ✅ **Aceptado y aplicado**

## 🔍 Memories Aprendidos pero No Comunicados

### 1. **Preferencia de Comandos en Inglés**
- **Descripción**: El usuario prefiere que los mensajes de commit de Git estén en inglés
- **Contexto**: Durante la configuración del primer commit
- **Evidencia**: "Además prefiero que los mensajes de los commits los hagamos en inglés"
- **Estado**: ✅ **Aplicado automáticamente**

### 2. **Enfoque Pragmático en Solución de Problemas**
- **Descripción**: El usuario prioriza soluciones prácticas y funcionales sobre perfección técnica
- **Contexto**: Múltiples decisiones durante el despliegue
- **Evidencia**: 
  - Cambio de App Platform a Droplet por costos
  - Uso de `|| true` en Dockerfile para bypass de errores
  - Priorización de funcionalidad sobre optimización
- **Estado**: ✅ **Aplicado automáticamente**

### 3. **Preferencia por Documentación Completa**
- **Descripción**: El usuario valora mucho la documentación exhaustiva y detallada
- **Contexto**: Solicitud de postmortem completo y documentación web
- **Evidencia**: "Lo siguiente que quiero que hagas es una documentación en formato postmortem de todo lo que hemos hecho"
- **Estado**: ✅ **Aplicado automáticamente**

### 4. **Atención a la Seguridad**
- **Descripción**: El usuario es muy consciente de los aspectos de seguridad
- **Contexto**: Revisión de información sensible en repositorios
- **Evidencia**: "revisa el codigo que se ha subido a ver si hay informacion sensible"
- **Estado**: ✅ **Aplicado automáticamente**

### 5. **Preferencia por Soluciones Simples y Directas**
- **Descripción**: El usuario prefiere soluciones simples y directas sobre complejidad innecesaria
- **Contexto**: Resolución del problema de renderizado de documentación
- **Evidencia**: "en lugar de copiar directamente los ficheros locales, con ese contenido crea los ficheros HTML"
- **Estado**: ✅ **Aplicado automáticamente**

## 🚫 Memories No Aceptados o Rechazados

### 1. **Uso de CDN Externo para Markdown**
- **Descripción**: Propuesta de usar `marked.js` desde CDN para renderizar markdown
- **Contexto**: Implementación del visor de documentación
- **Razón del rechazo**: Problemas de conectividad y dependencias externas
- **Estado**: ❌ **Rechazado - Reemplazado por solución standalone**

### 2. **Configuración Compleja de JavaScript**
- **Descripción**: Implementación de visor JavaScript complejo con navegación dinámica
- **Contexto**: Visor de documentación web
- **Razón del rechazo**: Problemas de compatibilidad y complejidad innecesaria
- **Estado**: ❌ **Rechazado - Reemplazado por HTML simple**

### 3. **Servido de Archivos Markdown Crudos**
- **Descripción**: Servir archivos markdown directamente desde Nginx
- **Contexto**: Configuración inicial de documentación web
- **Razón del rechazo**: Los navegadores no renderizan markdown automáticamente
- **Estado**: ❌ **Rechazado - Reemplazado por HTML renderizado**

## 📊 Patrones de Comportamiento Identificados

### 1. **Comunicación Técnica**
- **Estilo**: Directo y específico
- **Preferencias**: 
  - Comandos en inglés
  - Explicaciones claras y concisas
  - Soluciones prácticas

### 2. **Toma de Decisiones**
- **Enfoque**: Pragmático y basado en costos
- **Prioridades**:
  - Funcionalidad sobre perfección
  - Simplicidad sobre complejidad
  - Seguridad desde el inicio

### 3. **Gestión de Proyectos**
- **Metodología**: Iterativa y adaptativa
- **Características**:
  - Documentación exhaustiva
  - Separación de responsabilidades
  - Infraestructura como código

## 🎯 Memories Candidatos para Futuras Sesiones

### 1. **Preferencia por Soluciones Standalone**
- **Descripción**: El usuario prefiere soluciones que no dependan de servicios externos
- **Evidencia**: Rechazo de CDN externos y preferencia por archivos HTML autónomos
- **Aplicación**: Evitar dependencias externas en futuras implementaciones

### 2. **Valoración de la Limpieza de Código**
- **Descripción**: El usuario aprecia configuraciones limpias y sin elementos innecesarios
- **Evidencia**: Solicitud de eliminar referencias a markdown de Nginx
- **Aplicación**: Mantener configuraciones minimalistas y limpias

### 3. **Importancia de la Experiencia de Usuario**
- **Descripción**: El usuario valora la experiencia de usuario final
- **Evidencia**: Insistencia en que la documentación se vea correctamente renderizada
- **Aplicación**: Priorizar UX en todas las implementaciones

### 4. **Preferencia por Automatización**
- **Descripción**: El usuario valora la automatización de procesos
- **Evidencia**: Implementación de GitHub Actions para CI/CD
- **Aplicación**: Proponer automatización en futuros proyectos

## 🔮 Recomendaciones para Futuras Interacciones

### 1. **Comunicación Inicial**
- Confirmar preferencias de herramientas (sftp vs scp, etc.)
- Establecer expectativas sobre documentación
- Definir nivel de detalle requerido

### 2. **Desarrollo de Soluciones**
- Proponer soluciones simples primero
- Evitar dependencias externas innecesarias
- Priorizar funcionalidad sobre perfección

### 3. **Gestión de Proyectos**
- Documentar decisiones y configuraciones temprano
- Separar responsabilidades en repositorios diferentes
- Implementar seguridad desde el inicio

### 4. **Resolución de Problemas**
- Enfoque sistemático y estructurado
- Validar soluciones antes de implementar
- Mantener configuraciones limpias

## 📝 Conclusión

Los memories aprendidos durante esta interacción proporcionan una base sólida para futuras colaboraciones, permitiendo un enfoque más eficiente y alineado con las preferencias del usuario. La combinación de experiencia técnica, enfoque pragmático y atención a la calidad hace que el usuario sea un excelente colaborador para proyectos de infraestructura y despliegue.

---

**Nota**: Este documento debe actualizarse con cada nueva interacción para mantener un registro completo de las preferencias y patrones del usuario.

