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

## 📖 Uso del Repositorio
Este repositorio está diseñado como guía práctica para demostrar competencias en:  
- **Arquitectura en la nube**  
- **Automatización y scripting**  
- **Seguridad y control de accesos**  
- **Pruebas y documentación técnica**  


### Pre-requisitos 📋

_Creación de cuenta en Google Cloud_
![Pantalla de inicio](files/sesion.png)


```
Da un ejemplo
```

### Instalación 🔧

_Una serie de ejemplos paso a paso que te dice lo que debes ejecutar para tener un entorno de desarrollo ejecutandose_

_Dí cómo será ese paso_

```
Da un ejemplo
```

_Y repite_

```
hasta finalizar
```

_Finaliza con un ejemplo de cómo obtener datos del sistema o como usarlos para una pequeña demo_

## Ejecutando las pruebas ⚙️

_Explica como ejecutar las pruebas automatizadas para este sistema_

### Analice las pruebas end-to-end 🔩

_Explica que verifican estas pruebas y por qué_

```
Da un ejemplo
```

### Y las pruebas de estilo de codificación ⌨️

_Explica que verifican estas pruebas y por qué_

```
Da un ejemplo
```

## Despliegue 📦

_Agrega notas adicionales sobre como hacer deploy_

## Construido con 🛠️

_Menciona las herramientas que utilizaste para crear tu proyecto_

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - El framework web usado
* [Maven](https://maven.apache.org/) - Manejador de dependencias
* [ROME](https://rometools.github.io/rome/) - Usado para generar RSS

## Contribuyendo 🖇️

Por favor lee el [CONTRIBUTING.md](https://gist.github.com/villanuevand/xxxxxx) para detalles de nuestro código de conducta, y el proceso para enviarnos pull requests.

## Wiki 📖

Puedes encontrar mucho más de cómo utilizar este proyecto en nuestra [Wiki](https://github.com/tu/proyecto/wiki)

## Versionado 📌

Usamos [SemVer](http://semver.org/) para el versionado. Para todas las versiones disponibles, mira los [tags en este repositorio](https://github.com/tu/proyecto/tags).

## Autores ✒️

_Menciona a todos aquellos que ayudaron a levantar el proyecto desde sus inicios_

* **Andrés Villanueva** - *Trabajo Inicial* - [villanuevand](https://github.com/villanuevand)
* **Fulanito Detal** - *Documentación* - [fulanitodetal](#fulanito-de-tal)

También puedes mirar la lista de todos los [contribuyentes](https://github.com/your/project/contributors) quíenes han participado en este proyecto. 

## Licencia 📄

Este proyecto está bajo la Licencia (Tu Licencia) - mira el archivo [LICENSE.md](LICENSE.md) para detalles

## Expresiones de Gratitud 🎁

* Comenta a otros sobre este proyecto 📢
* Invita una cerveza 🍺 o un café ☕ a alguien del equipo. 
* Da las gracias públicamente 🤓.
* Dona con cripto a esta dirección: `0xf253fc233333078436d111175e5a76a649890000`
* etc.
