# ADR-002: Incorporación de una Capa de Caché (Redis) para mitigar la Alta Latencia en el Listado Público

* **Estado:** Aceptado
* **Fecha:** 2026-07-05[cite: 2]
* **Decisores:** Mendez Carla, Avellaneda Marian[cite: 1, 2]
* **Relacionado:** SPEC-01 (HU-01), Issue #8[cite: 1, 2]

## Contexto
* La historia de usuario HU-01 define un listado público de eventos académicos que debe soportar paginación (20 por página) y múltiples filtros dinámicos concurrentes (por tipo, modo, próximos o finalizados)[cite: 1].
* Al tratarse de la vista principal expuesta tanto a visitantes anónimos como a usuarios registrados, se prevé que represente más del 80% del tráfico de lectura del sistema[cite: 1].
* El crecimiento constante de la base de datos de eventos históricos combinada con búsquedas por términos de descripción generará una latencia alta en las consultas del motor relacional, degradando el rendimiento general[cite: 1, 2].

## Decisión
* Se decide incorporar un nuevo componente a la arquitectura: una capa de caché en memoria distribuida utilizando Redis[cite: 2].
* El alcance de la decisión cubre la optimización exclusiva de las consultas de lectura pesada del listado público y búsquedas frecuentes con filtros de la HU-01[cite: 1, 2].
* No cubre ni almacena datos de eventos en estado "borrador" ni interfiere de forma directa en los procesos transaccionales de inscripción de participantes de la SPEC-02[cite: 1].

## Alternativas consideradas

### Opción A: Optimización exclusiva de índices en la Base de Datos Relacional
* **Pros:** No introduce nuevos componentes de software ni añade complejidad a la infraestructura del sistema[cite: 2].
* **Contras:** No resuelve de forma definitiva el problema de latencia ante picos de tráfico masivo concurrente de visitantes, ya que el servidor de base de datos seguirá procesando cada petición de búsqueda de texto y ordenamiento repetitivamente[cite: 1, 2].

### Opción B: Capa de Caché en Memoria Local de la Aplicación (In-Memory Cache)
* **Pros:** Implementación extremadamente rápida y directa sin dependencias de servicios externos al backend[cite: 2].
* **Contras:** Limita severamente la capacidad de escalabilidad horizontal del sistema, dado que cada instancia del servidor web tendría un estado de caché desincronizado, violando la consistencia del listado de eventos[cite: 1, 2].

### Opción C: Capa de Caché Distribuida con Redis
* **Pros:** Reduce drásticamente la latencia de respuesta al recuperar listas precalculadas directamente desde la memoria RAM, liberando de carga computacional a la base de datos PostgreSQL y permitiendo que el módulo escale de forma eficiente[cite: 2].
* **Contras:** Requiere controlar e implementar con precisión políticas estrictas de invalidación de caché ante modificaciones de datos[cite: 2].

## Consecuencias
* **Beneficios esperados:** Tiempos de respuesta óptimos y baja latencia en la pantalla principal pública, garantizando una buena experiencia al visitante[cite: 1, 2].
* **Costos o riesgos que se aceptan:** Incremento menor en los costos de infraestructura operativa y esfuerzo de desarrollo extra para asegurar que los cambios de estado (como publicar o editar) limpien el caché viejo[cite: 1, 2].
* **Impacto en operación y equipo:** El equipo de desarrollo deberá familiarizarse con patrones de diseño de software para la gestión e invalidación correcta de claves de caché[cite: 2].

## Plan de implementación
* Desplegar un contenedor de Redis en el entorno integrado de desarrollo local[cite: 2].
* Modificar el servicio del listado público para aplicar el patrón "Cache-Aside": verificar primero en Redis si existe la combinación de filtros/página solicitada; de lo contrario, consultar la base de datos y poblar el caché con un tiempo de vida (TTL) predeterminado[cite: 1, 2].
* Configurar la invalidación de claves de caché dentro de los comandos de escritura de eventos, asegurando que al crear, editar, publicar o eliminar un evento (HU-03 a HU-06), el listado público se actualice de inmediato[cite: 1, 2].

## Dependencias
* Disponibilidad del componente Redis en los entornos de desarrollo y producción[cite: 2].

## Métrica de éxito
* Lograr que el tiempo de respuesta en peticiones concurrentes al listado público filtrado se mantenga estable por debajo de un umbral aceptable (ej. < 80 ms), reduciendo en un 70% las consultas directas a la base de datos principal[cite: 2].

## Triggers de revisión
* Detección de fallos graves de consistencia donde los usuarios visualicen eventos desactualizados o eventos en estado "borrador" en el listado público por errores en la lógica de invalidación[cite: 1, 2].
* **Fecha sugerida de revisión:** 2026-11-20[cite: 2].
