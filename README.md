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
## 🛠️ 1. Creación y configuración del proyecto


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

## 📌 Habilitación de APIs desde la Google Cloud Console

O bien, empleando la **Google Cloud Console**:

1. En el menú lateral, selecciona **APIs y Servicios**. ![Pantalla de inicio](files/habilitar.png)
2. En la sección **APIs y Servicios habilitados**, identifica cuáles APIs ya están activas. ![Pantalla de inicio](files/habilitados.png)
3. Si necesitas agregar una nueva, selecciona nuevamente **APIs y Servicios → Biblioteca** y habilita la API correspondiente. Cuando se habilita una nueva API desde la **Google Cloud Console**, el cambio se refleja inmediatamente en la sección **APIs y Servicios habilitados**. Las APIs activas aparecen destacadas en la lista con un estado diferente al de las que aún no están habilitadas, lo que permite identificar fácilmente cuáles ya están disponibles en el proyecto.
![Pantalla de inicio](files/verificacion.png)

## 🔐 Configuración de Roles y Permisos (IAM)

Para simular un entorno de producción controlado, se recomienda crear un **usuario de prueba con privilegios mínimos** utilizando **Identity and Access Management (IAM)**.

### 📖 Referencia
Puedes revisar la documentación oficial de Google Cloud IAM para comprender cómo funciona la gestión de identidades y accesos:  
[Documentación IAM - Google Cloud](https://docs.cloud.google.com/iam/docs/overview?hl=es-419)

### 🛠️ Pasos en la Google Cloud Console
En la **Google Cloud Console**, la gestión de accesos se realiza desde la sección **IAM y Administración → IAM**.

1. Haz clic en **Otorgar acceso** para añadir un nuevo miembro (usuario o cuenta de servicio). ![Pantalla de inicio](files/otorgar.png)
2. Ingresa el correo electrónico del usuario de prueba.  ![Pantalla de inicio](files/creacion.png) 
3. Selecciona un rol con privilegios mínimos, por ejemplo:
   - **Viewer** → acceso de solo lectura a todo el proyecto.
   - **Storage Object Viewer** → acceso de solo lectura a objetos en Cloud Storage.
4. Guarda los cambios. El nuevo usuario aparecerá en la lista con los permisos asignados. ![Pantalla de inicio](files/nuevo-usu.png)
5. En caso de ser necesario, se pueden editar los permisos ![Pantalla de inicio](files/edit.png)

✅ Con estos pasos, el usuario de prueba tendrá acceso restringido, lo que permite simular un entorno seguro y controlado.

## 🔐 Buenas prácticas de asignación de privilegios

Para garantizar un entorno controlado y seguro en producción, se recomienda aplicar el principio de **privilegios mínimos** al asignar roles y permisos en IAM.  
Esto significa otorgar únicamente los permisos estrictamente necesarios para cada usuario o cuenta de servicio.

📖 Para más detalles sobre buenas prácticas de seguridad en IAM, consulta la documentación oficial:  
[Usar IAM de forma segura - Google Cloud](https://docs.cloud.google.com/iam/docs/using-iam-securely?hl=es-419)

---
# 2. Diseño de Almacenamiento y Automatización ⚙️
---

##  Crear el bucket en Cloud Storage

1. Ingresa a [console.cloud.google.com](https://console.cloud.google.com) y selecciona el proyecto.
2. Ve a **Cloud Storage → Buckets → Crear**.
3. Configura los siguientes parámetros:

| Paso | Configuración recomendada |
|---|---|
| **Nombre** | Único globalmente, por ejemplo `example-bucket-prueba-01` |
| **Ubicación** | Región, birregión o multirregión según latencia, costo y residencia de datos |
| **Clase de almacenamiento** | `Standard` (las reglas de ciclo de vida la cambiarán después) |
| **Control de acceso** | Activar **Prevenir acceso público** y elegir **Uniforme** |
| **Protección de datos** | Activar **control de versiones**, definir **política de retención** y revisar *soft delete* |

4. Haz clic en **Crear**.

> **Nota:** con el control de acceso uniforme, todo el acceso se gestiona únicamente con IAM, sin ACL por objeto.

---

## Configurar reglas de ciclo de vida

1. Abre el bucket y entra a la pestaña **Ciclo de vida**.
2. Haz clic en **Agregar una regla**.
3. Define la **acción**:
   - Establecer la clase de almacenamiento (Nearline, Coldline o Archive).
   - Borrar objeto.
   - Borrar versiones no actuales (requiere control de versiones).
   - Cancelar cargas multiparte incompletas.
4. Define las **condiciones**, por ejemplo:
   - Edad (días desde la creación).
   - Creado antes de una fecha.
   - Clase de almacenamiento actual.
   - Cantidad de versiones más recientes.
   - Días desde que el objeto pasó a no actual.
5. Guarda la regla.

### Regla para Archivos

| Regla | Condición | Acción |
|---|---|---|
| 1 | Edad ≥ 30 días | Pasar a Nearline |
| 2 | Edad ≥ 90 días | Pasar a Coldline |
| 3 | Edad ≥ 365 días | Borrar objeto |
| 4 | Más de 3 versiones, no actuales | Borrar versión no actual |

> Las reglas pueden tardar hasta 24 horas en aplicarse.

### Política de retención

En la pestaña **Protección → Política de retención** se define un período mínimo durante el cual los objetos no pueden borrarse ni sobrescribirse.

- Una regla de ciclo de vida que borra no eliminará un objeto hasta que termine su período de retención.
- **Bloquear** la política de retención es **irreversible**. Confirma el período antes de hacerlo.

---

## Configurar políticas de acceso (IAM)

1. En el bucket, abre la pestaña **Permisos**.
2. Haz clic en **Otorgar acceso**.
3. En **Nuevos principales**, escribe un **grupo** (preferible) o una **cuenta de servicio**.
4. Selecciona el rol según la necesidad:

| Necesidad | Rol |
|---|---|
| Solo lectura | `roles/storage.objectViewer` |
| Subir objetos sin borrar ni sobrescribir | `roles/storage.objectCreator` |
| Leer, escribir y borrar objetos | `roles/storage.objectUser` |
| Control total de objetos | `roles/storage.objectAdmin` |
| Administrar el bucket y su IAM | `roles/storage.admin` (solo administradores) |

5. Opcional: usa **Agregar condición de IAM** para que el acceso expire en una fecha o aplique solo a un prefijo de objetos.
6. Guarda los cambios.

### Esquema 

| Principal | Rol | Propósito |
|---|---|---|
| `grupo-lectura@empresa.com` | `Storage Object Viewer` | Consulta de datos |
| `sa-app@proyecto.iam.gserviceaccount.com` | `Storage Object Creator` u `Object User` | Escritura desde la aplicación |
| `grupo-admin-storage@empresa.com` | `Storage Admin` | Administración (grupo reducido) |

---

## Buenas prácticas de seguridad

- Otorga los roles **a nivel de bucket**, no de proyecto (menor alcance posible).
- Evita los roles básicos (Owner, Editor, Viewer) en producción.
- Asigna roles a **grupos** en lugar de usuarios individuales.
- Usa una **cuenta de servicio distinta** por cada aplicación o componente.
- Evita las **claves de cuenta de servicio**. Si son indispensables, rótalas y no las guardes en el código.
- Mantén activo **Prevenir acceso público**.
- Usa **condiciones de IAM** o acceso temporal para permisos que no deben ser permanentes.
- Revisa periódicamente los **Registros de auditoría de Cloud** para detectar cambios en políticas y accesos.

---

## Referencias

- [Usa IAM de forma segura](https://docs.cloud.google.com/iam/docs/using-iam-securely?hl=es-419)
- [Documentación de Cloud Storage](https://cloud.google.com/storage/docs?hl=es-419)


---
