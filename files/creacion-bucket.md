# Bucket de Cloud Storage con ciclo de vida y accesos refinados

Guía para crear y configurar un bucket de Cloud Storage en un entorno de producción controlado desde la **Google Cloud Console**, aplicando el principio de **privilegio mínimo**.

## Objetivo

- Crear un bucket seguro (sin acceso público y con control de acceso uniforme).
- Definir reglas de **ciclo de vida** para gestionar la retención y eliminación de archivos.
- Configurar **políticas de acceso refinadas** con roles IAM específicos de lectura y escritura.

## Requisitos previos

- Un proyecto de Google Cloud con facturación activa.
- La API de Cloud Storage habilitada.
- Permisos para crear buckets (por ejemplo `roles/storage.admin` en el proyecto) y para administrar IAM del bucket.
- Grupos de Google o cuentas de servicio definidos para asignar los roles.

---

## 1. Crear el bucket

Ruta en la consola: **Cloud Storage → Buckets → Crear**.

> Los valores de la columna **Valor sugerido** son una referencia para un entorno de producción controlado. Ajústalos a los requisitos reales y deja registrado el valor final y su justificación.

### Paso 1. Asignar un nombre al bucket

| Opción | Descripción | Valor sugerido | Justificación |
|---|---|---|---|
| **Nombre del bucket** | Identificador permanente y único en todo Google Cloud. Admite minúsculas, números, guiones, guiones bajos y puntos. No se puede cambiar después y es visible en las URL, por lo que no debe contener información sensible. | `empresa-prod-datos-01` | Convención `empresa-entorno-propósito-número` para identificar el bucket con facilidad. |
| **Etiquetas** (opcional) | Pares clave-valor para organizar recursos, filtrar y analizar costos. No afectan los permisos. | `entorno:prod`, `area:<área>` | Facilitan el seguimiento de costos y la gestión del inventario. |
| **Espacio de nombres jerárquico** (opcional) | Organiza los objetos como carpetas reales y acelera operaciones sobre carpetas. Útil en cargas de análisis de datos y machine learning. Solo se define al crear el bucket y tiene limitaciones de compatibilidad. | Desactivado | No hay un caso de uso que lo requiera. |

### Paso 2. Elegir dónde almacenar los datos

La ubicación no se puede cambiar después de crear el bucket.

| Tipo de ubicación | Descripción | Cuándo usarla |
|---|---|---|
| **Multirregión** | Replica los datos en varias regiones de un área amplia (por ejemplo US, EU, ASIA). Máxima disponibilidad y mayor costo. | Contenido distribuido globalmente. |
| **Birregión** | Replica entre dos regiones concretas. Equilibra disponibilidad, rendimiento y control de ubicación. Admite replicación turbo. | Alta disponibilidad con control sobre las regiones. |
| **Región** | Los datos permanecen en una sola región. Menor costo y menor latencia si las aplicaciones están en la misma región. | Requisitos de residencia de datos o costo reducido. |

| Opción | Valor sugerido | Justificación |
|---|---|---|
| **Tipo de ubicación** | Región | Cercanía a la aplicación y cumplimiento de residencia de datos. |
| **Ubicación** | `us-central1` *(ajustar)* | Misma región que los servicios que consumen el bucket, para reducir latencia y costos de salida de datos. |

### Paso 3. Elegir una clase de almacenamiento

| Opción | Descripción | Valor sugerido |
|---|---|---|
| **Autoclass** | Google mueve cada objeto entre clases según su patrón de acceso, sin cargos por recuperación. Ideal si el acceso es impredecible. | Desactivado |
| **Clase predeterminada** | Clase que se asigna a todos los objetos nuevos. | **Standard** |

| Clase | Uso típico | Duración mínima |
|---|---|---|
| **Standard** | Datos de acceso frecuente | Ninguna |
| **Nearline** | Lectura aproximadamente una vez al mes | 30 días |
| **Coldline** | Lectura aproximadamente una vez por trimestre | 90 días |
| **Archive** | Respaldos y archivado de largo plazo (menos de una vez al año) | 365 días |

**Justificación:** se inicia en Standard y las reglas de ciclo de vida (sección 2) mueven los objetos a clases más frías con el tiempo. Las clases más frías cuestan menos por almacenamiento, pero más por recuperación, y cobran la duración mínima aunque el objeto se borre antes. Todas ofrecen acceso en milisegundos y la misma durabilidad.

### Paso 4. Elegir cómo controlar el acceso

| Opción | Descripción | Valor sugerido | Justificación |
|---|---|---|---|
| **Aplicar prevención de acceso público** | Impide que el contenido se exponga a internet, incluso si alguien intenta otorgar permisos públicos por error. | **Activada** | Evita exposición accidental de datos en producción. |
| **Modelo de control de acceso: Uniforme** | Todo el acceso se gestiona solo con IAM a nivel de bucket. Más simple y auditable. | **Uniforme** | Control centralizado con IAM y alineado con el privilegio mínimo. |
| **Modelo de control de acceso: Detallado** | Combina IAM con listas de control de acceso (ACL) por objeto. Más difícil de auditar. | No usar | Solo se justifica por compatibilidad con sistemas heredados. |

### Paso 5. Elegir cómo proteger los datos de los objetos

| Opción | Descripción | Valor sugerido | Justificación |
|---|---|---|---|
| **Eliminación temporal (soft delete)** | Conserva los objetos borrados durante un periodo (por defecto 7 días) para poder restaurarlos. Los objetos conservados siguen generando costo. | 7 días *(ajustar)* | Permite recuperar borrados accidentales con un costo acotado. |
| **Control de versiones** | Guarda versiones anteriores cuando un objeto se sobrescribe o se borra. Aumenta el consumo de almacenamiento. | Activado | Protege contra sobrescrituras accidentales. Se acompaña de una regla de ciclo de vida que limita las versiones. |
| **Retención a nivel de bucket** | Define un periodo mínimo en que los objetos no pueden borrarse ni sobrescribirse. Puede bloquearse. | Según requisito normativo *(definir)* | Cumplimiento y protección de datos críticos. |
| **Retención a nivel de objeto** | Permite periodos de retención distintos por objeto. Debe habilitarse al crear el bucket. | Desactivada | No se requieren periodos por objeto. |
| **Cifrado: clave administrada por Google** | Google cifra los datos y gestiona las claves de forma automática. | **Predeterminado** | No hay requisito normativo de gestionar claves propias. |
| **Cifrado: clave administrada por el cliente (CMEK)** | Se usa una clave propia en Cloud KMS, con control de rotación, desactivación y auditoría. Si la clave se pierde o se deshabilita, los datos dejan de ser legibles. | Solo si la normativa lo exige | Añade responsabilidad operativa. |

### Paso 6. Crear

Al hacer clic en **Crear** se aplican las opciones anteriores.

### Decisiones irreversibles o difíciles de cambiar

Confirma estos puntos antes de crear el bucket y registra el valor elegido:

- **Nombre del bucket:** no se puede cambiar.
- **Ubicación:** no se puede cambiar.
- **Espacio de nombres jerárquico:** solo se define en la creación.
- **Bloqueo de la política de retención:** es irreversible; no se podrá reducir ni quitar.
- **Control de acceso uniforme:** pasados 90 días, ya no se puede volver al modelo detallado.
- **Clave CMEK:** si se pierde o se deshabilita, no se podrán leer los datos.

---

## 2. Configurar reglas de ciclo de vida

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

### Ejemplo de reglas encadenadas

| Regla | Condición | Acción |
|---|---|---|
| 1 | Edad ≥ 30 días | Pasar a Nearline |
| 2 | Edad ≥ 90 días | Pasar a Coldline |
| 3 | Edad ≥ 365 días | Borrar objeto |
| 4 | Más de 3 versiones, no actuales | Borrar versión no actual |

> Las reglas pueden tardar hasta 24 horas en aplicarse.

### Política de retención

En la pestaña **Protección → Política de retención** se define un periodo mínimo durante el cual los objetos no pueden borrarse ni sobrescribirse.

- Una regla de ciclo de vida que borra no eliminará un objeto hasta que termine su periodo de retención.
- **Bloquear** la política de retención es **irreversible**. Confirma el periodo antes de hacerlo.

---

## 3. Configurar políticas de acceso (IAM)

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

### Esquema sugerido

| Principal | Rol | Propósito |
|---|---|---|
| `grupo-lectura@empresa.com` | `Storage Object Viewer` | Consulta de datos |
| `sa-app@proyecto.iam.gserviceaccount.com` | `Storage Object Creator` u `Object User` | Escritura desde la aplicación |
| `grupo-admin-storage@empresa.com` | `Storage Admin` | Administración (grupo reducido) |

---

## 4. Buenas prácticas de seguridad

- Otorga los roles **a nivel de bucket**, no de proyecto (menor alcance posible).
- Evita los roles básicos (Owner, Editor, Viewer) en producción.
- Asigna roles a **grupos** en lugar de usuarios individuales.
- Usa una **cuenta de servicio distinta** por cada aplicación o componente.
- Evita las **claves de cuenta de servicio**. Si son indispensables, rótalas y no las guardes en el código.
- Mantén activa la **prevención de acceso público**.
- Usa **condiciones de IAM** o acceso temporal para permisos que no deben ser permanentes.
- Revisa periódicamente los **Registros de auditoría de Cloud** para detectar cambios en políticas y accesos.

---

## 5. Verificación

- [ ] La **prevención de acceso público** está activa.
- [ ] El control de acceso es **Uniforme**.
- [ ] La ubicación y la clase predeterminada coinciden con lo documentado.
- [ ] El control de versiones, *soft delete* y la retención están configurados según lo acordado.
- [ ] Las reglas de ciclo de vida aparecen en la pestaña **Ciclo de vida**.
- [ ] Los roles de la pestaña **Permisos** corresponden solo a los grupos y cuentas necesarios.
- [ ] No hay principales con roles más amplios de lo requerido.

---

## Referencias

- [Usa IAM de forma segura](https://docs.cloud.google.com/iam/docs/using-iam-securely?hl=es-419)
- [Documentación de Cloud Storage](https://cloud.google.com/storage/docs?hl=es-419)
