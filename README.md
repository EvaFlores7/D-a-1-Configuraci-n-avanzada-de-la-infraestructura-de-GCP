# Día 1: Configuración avanzada de la infraestructura de GCP

_Este repositorio documenta la creación y configuración de un entorno seguro y escalable en Google Cloud Platform (GCP), poniendo a prueba habilidades en scripting, manejo de APIs y diseño de arquitectura en la nube._

## 🎯 Objetivos
- Diseñar un proyecto en GCP con prácticas de seguridad y escalabilidad.  
- Implementar automatización mediante **Cloud Functions** y **Cloud Storage**.  
- Validar el flujo de trabajo con pruebas unitarias y documentación

---

## 📂 Alcance del Proyecto

### 1. Creación y Configuración del Proyecto
- Configuración inicial de un proyecto en GCP.  
- Habilitación de APIs clave:  
  - Cloud Storage  
  - Cloud Functions  
  - Cloud Pub/Sub  
  - Cloud Logging  
  - (Opcional) Cloud Build  
- Implementación de **IAM** con roles y permisos mínimos, incluyendo un usuario de prueba para simular un entorno controlado de producción.  

### 2. Diseño de Almacenamiento y Automatización
- Creación de un **bucket en Cloud Storage** con:  
  - Reglas de ciclo de vida para retención y eliminación de archivos.  
  - Políticas de acceso refinadas para lectura/escritura.  
- Desarrollo de una **Cloud Function (Python/Node.js)** que se activa al subir un archivo:  
  - Extracción de metadatos (nombre, tamaño, tipo).  
  - Registro de eventos en **Cloud Logging**.  
  - Manejo robusto de errores y logging detallado para depuración.  

### 3. Pruebas y Documentación
- Implementación de pruebas unitarias básicas para validar:  
  - Flujo exitoso de la función.  
  - Manejo de errores en escenarios controlados.  
- Documentación del proceso y resultados para asegurar reproducibilidad.  

---

## 🛠️ Tecnologías Utilizadas
- **Google Cloud Platform (GCP)**  
- **Cloud Storage, Cloud Functions, Pub/Sub, Logging, IAM**  
- **Python / Node.js**  
- **Unit Testing Frameworks**  

---

### Pre-requisitos 📋

_Creación de cuenta en Google Cloud_
![Pantalla de inicio](files/sesion.png)
---
## 1. Creación y configuración del proyecto


### 🔧 Instalación de Google Cloud CLI

Sigue la [documentación oficial de Google Cloud](https://cloud.google.com/sdk/docs/install) para instalar la CLI en tu sistema operativo.

### Pasos básicos:
1. Descargar el instalador según tu plataforma (Windows, Linux, macOS).
2. Ejecutar el instalador o script de instalación. ![Pantalla de inicio](files/instalacion.png)

3. Inicializar la CLI con `gcloud init`. ![Pantalla de inicio](files/init.png)
Esto abrirá un asistente interactivo para:
- Seleccionar tu cuenta de Google.
- Elegir el proyecto por defecto.

4. Configurar la región y zona predeterminadas. ![Pantalla de inicio](files/region.png)
5. Verificar instalación con `gcloud --version`.
6. Autenticarse con `gcloud auth login` si es necesario.

## ⚙️ Habilitación de APIs en GCP

Para que el proyecto funcione correctamente, es necesario habilitar las siguientes APIs en Google Cloud:

- **Cloud Storage**
- **Cloud Functions**
- **Cloud Pub/Sub**
- **Cloud Logging**
- *(Opcional)* **Cloud Build**

Ejecuta los siguientes comandos en tu terminal de Google Cloud SDK:

```bash
gcloud services enable storage.googleapis.com
gcloud services enable cloudfunctions.googleapis.com
gcloud services enable pubsub.googleapis.com
gcloud services enable logging.googleapis.com
# Opcional
gcloud services enable cloudbuild.googleapis.com

```
✅ Estos comandos habilitan las APIs necesarias para que tu entorno pueda crear buckets, desplegar funciones, manejar eventos y registrar logs.

## 🔐 2. Configurar roles y permisos mediante IAM,(IAM)

## Diseño de Almacenamiento y Automatización ⚙️

_Explica como ejecutar las pruebas automatizadas para este sistema_


