# SPEC-01: Gestión y Listado Público de Eventos

## 1. Objetivo y Contexto

### Objetivo
Implementar la funcionalidad completa de gestión de eventos académicos, permitiendo a los organizadores crear, editar, eliminar y publicar eventos, mientras que cualquier usuario (autenticado o no) puede consultar el listado público de eventos con filtros y ver detalles individuales.

### Contexto
El sistema Academic Event Manager requiere un módulo central de eventos que sea la base de todas las demás funcionalidades. Los eventos académicos (cursos, jornadas, congresos, charlas, talleres, seminarios) tienen características específicas como fechas de inicio/fin, períodos de inscripción, capacidades mínimas/máximas y diferentes modos de realización (presencial, virtual, híbrido).

Este módulo es fundamental porque:
- Es el punto de entrada principal para los participantes
- Determina las reglas de negocio para inscripciones
- Define la estructura sobre la que se construyen roles, certificados y encuestas

### Alcance
- CRUD completo de eventos (solo organizadores)
- Listado público con paginación y filtros
- Vista detalle de evento individual
- Cambio de estado del evento (borrador -> publicado -> en_curso -> finalizado)
- Visualización de agenda del evento

---

## 2. Historias de Usuario y Criterios de Aceptación

### HU-01: Listado Público de Eventos
**Como** visitante o usuario registrado  
**Quiero** ver un listado de eventos académicos con filtros  
**Para** encontrar eventos de mi interés  

**Criterios de Aceptación:**
- Dado que accedo al listado de eventos, entonces veo los eventos publicados ordenados por fecha de inicio más próxima
- Dado que aplico un filtro por tipo, entonces solo veo eventos de ese tipo
- Dado que aplico un filtro por modo (presencial/virtual/híbrido), entonces solo veo eventos en ese modo
- Dado que aplico el filtro "próximos", entonces solo veo eventos con fecha de inicio futura
- Dado que aplico el filtro "finalizados", entonces solo veo eventos que ya pasaron
- Dado que uso el buscador, entonces veo eventos cuyo título o descripción contengan el término buscado
- Dado que hay más de 20 eventos, entonces veo paginación con 20 eventos por página
- Dado que un evento está en estado "borrador", entonces NO aparece en el listado público

### HU-02: Ver Detalle de Evento
**Como** visitante o usuario registrado  
**Quiero** ver los detalles completos de un evento  
**Para** decidir si quiero inscribirme  

**Criterios de Aceptación:**
- Dado que accedo al detalle de un evento, entonces veo título, descripción, tipo, fechas, ubicación y modo
- Dado que el evento tiene capacidad definida, entonces veo el cupo disponible (maxCapacity - inscriptos confirmados)
- Dado que el evento tiene fechas de inscripción definidas, entonces veo si la inscripción está abierta o cerrada
- Dado que el evento tiene imagen, entonces veo la imagen del evento
- Dado que el evento está finalizado, entonces veo un badge indicando "Finalizado"

### HU-03: Crear Evento
**Como** usuario registrado  
**Quiero** crear un nuevo evento académico  
**Para** organizar una actividad académica  

**Criterios de Aceptación:**
- Dado que accedo a crear un evento, entonces veo un formulario con todos los campos requeridos
- Dado que completo el formulario con datos válidos, entonces el evento se crea en estado "borrador"
- Dado que no completo un campo requerido, entonces veo un mensaje de error indicando el campo faltante
- Dado que la fecha de fin es anterior a la de inicio, entonces veo un error de validación
- Dado que la fecha de fin de inscripción es posterior a la fecha de inicio del evento, entonces veo un error de validación
- Dado que maxCapacity es menor que minCapacity, entonces veo un error de validación
- Dado que creo un evento exitosamente, entonces soy asignado automáticamente como organizador del evento

### HU-04: Editar Evento
**Como** organizador de un evento  
**Quiero** modificar la información de mi evento  
**Para** mantener los datos actualizados  

**Criterios de Aceptación:**
- Dado que soy organizador del evento, entonces puedo editar todos los campos del evento
- Dado que no soy organizador, entonces no puedo acceder a la edición del evento (403)
- Dado que el evento está "en_curso", entonces solo puedo modificar descripción e imagen
- Dado que el evento está "finalizado", entonces no puedo modificar ningún campo
- Dado que guardo los cambios exitosamente, entonces veo los datos actualizados

### HU-05: Publicar Evento
**Como** organizador de un evento  
**Quiero** cambiar el estado de mi evento de "borrador" a "publicado"  
**Para** que sea visible en el listado público  

**Criterios de Aceptación:**
- Dado que mi evento está en "borrador" y completo los datos mínimos, entonces puedo publicarlo
- Dado que publico un evento, entonces aparece en el listado público
- Dado que el evento no tiene título o descripción, entonces no puedo publicarlo
- Dado que el evento ya está publicado, entonces no veo la opción de publicar

### HU-06: Eliminar Evento
**Como** organizador de un evento  
**Quiero** eliminar un evento  
**Para** cancelar un evento que no se realizará  

**Criterios de Aceptación:**
- Dado que el evento está en "borrador", entonces puedo eliminarlo sin restricciones
- Dado que el evento tiene inscripciones confirmadas, entonces debo confirmar la eliminación y se notifica a los inscriptos
- Dado que el evento está "en_curso" o "finalizado", entonces no puedo eliminarlo (403)
- Dado que elimino un evento, entonces se aplica soft delete (deletedAt)

---

## 3. Requisitos Funcionales y Reglas de Negocio

### Requisitos Funcionales
- **RF-01**: El sistema debe permitir crear eventos con los siguientes campos: título, descripción, tipo, fecha inicio, fecha fin, ubicación, modo, capacidad mínima (opcional), capacidad máxima (opcional), fecha inicio inscripción, fecha fin inscripción, imagen (opcional)
- **RF-02**: El sistema debe permitir editar eventos existentes respetando las restricciones de estado
- **RF-03**: El sistema debe permitir eliminar eventos con soft delete
- **RF-04**: El sistema debe listar eventos públicos con paginación (20 por página por defecto)
- **RF-05**: El sistema debe permitir filtrar eventos por tipo, modo, estado (próximo/en_curso/finalizado) y término de búsqueda
- **RF-06**: El sistema debe mostrar el detalle completo de un evento individual
- **RF-07**: El sistema debe calcular y mostrar el cupo disponible en tiempo real
- **RF-08**: El sistema debe validar que las fechas sean coherentes antes de crear/editar
- **RF-09**: El sistema debe asignar automáticamente el rol de organizador al creador del evento
- **RF-10**: El sistema debe permitir cambiar el estado del evento según el flujo: borrador -> publicado -> en_curso -> finalizado

### Reglas de Negocio
- **RN-01**: Un evento solo puede ser creado por usuarios autenticados
- **RN-02**: La fecha de inicio de inscripción debe ser posterior o igual a la fecha actual
- **RN-03**: La fecha de fin de inscripción debe ser anterior a la fecha de inicio del evento
- **RN-04**: Si maxCapacity está definido, debe ser mayor que minCapacity
- **RN-05**: Un evento en estado "borrador" no es visible en el listado público
- **RN-06**: Un evento publicado solo puede avanzar a "en_curso" cuando la fecha de inicio se alcanza
- **RN-07**: Un evento no puede volver a un estado anterior (no se puede "despublicar")
- **RN-08**: Solo el organizador puede modificar o eliminar su evento
- **RN-09**: Un evento con inscripciones confirmadas no puede ser eliminado sin confirmación explícita
- **RN-10**: Los campos título, descripción, tipo, fechas y ubicación son obligatorios

---

## 4. Restricciones Técnicas Específicas

### Backend
- Implementar endpoints RESTful según Contracts.md sección 2
- Validar todos los inputs con Zod schemas
- Implementar soft delete con campo `deletedAt` en Prisma
- Usar transacciones de Prisma al crear evento + asignar rol de organizador
- Indexar campos de búsqueda: `title`, `type`, `mode`, `status`, `startDate`
- Implementar paginación con cursor o offset según conveniencia

### Frontend
- Crear componente `EventList` con filtros laterales y grid de tarjetas
- Crear componente `EventCard` reusable para listado
- Crear componente `EventDetail` con toda la información del evento
- Crear componente `EventForm` para creación y edición (compartido)
- Implementar debounce en el campo de búsqueda (300ms)
- Usar React Query para caching de listados
- Implementar skeleton loaders durante carga de datos
- Responsive: grid de 3 columnas (desktop), 2 (tablet), 1 (mobile)

### Base de Datos
- Tabla `events` con índices en: `status`, `type`, `mode`, `startDate`
- Full-text search en `title` y `description` (PostgreSQL tsvector)
- Trigger para actualizar `updatedAt` automáticamente

### Performance
- Listado de eventos: respuesta < 200ms para 1000 registros
- Detalle de evento: respuesta < 100ms
- Implementar cache HTTP para listado público (Cache-Control: public, max-age=60)

---

## 5. Modelo de Datos

```prisma
model Event {
  id                      String    @id @default(uuid())
  title                   String
  description             String
  type                    EventType
  startDate               DateTime
  endDate                 DateTime
  location                String
  mode                    EventMode
  minCapacity             Int?
  maxCapacity             Int?
  registrationStartDate   DateTime
  registrationEndDate     DateTime
  organizerId             String
  status                  EventStatus @default(borrador)
  imageUrl                String?
  createdAt               DateTime  @default(now())
  updatedAt               DateTime  @updatedAt
  deletedAt               DateTime?

  organizer               User      @relation("EventOrganizer", fields: [organizerId], references: [id])
  registrations           Registration[]
  roles                   UserRole[]
  surveys                 Survey[]
  comments                Comment[]

  @@index([status])
  @@index([type])
  @@index([mode])
  @@index([startDate])
  @@index([deletedAt])
  @@map("events")
}

enum EventType {
  curso
  jornada
  congreso
  charla
  taller
  seminario
}

enum EventMode {
  presencial
  virtual
  hibrido
}

enum EventStatus {
  borrador
  publicado
  en_curso
  finalizado
  cancelado
}
```

### Modelo de Respuesta API (TypeScript)

```typescript
interface EventResponse {
  id: string;
  title: string;
  description: string;
  type: EventType;
  startDate: string;
  endDate: string;
  location: string;
  mode: EventMode;
  minCapacity?: number;
  maxCapacity?: number;
  registrationStartDate: string;
  registrationEndDate: string;
  organizerId: string;
  status: EventStatus;
  imageUrl?: string;
  createdAt: string;
  updatedAt: string;
  // Campos calculados
  availableSpots?: number;
  isRegistrationOpen?: boolean;
  organizer?: {
    firstName: string;
    lastName: string;
  };
}

interface EventListResponse {
  events: EventResponse[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
}
```

---

## 6. Plan de Tareas

### Tarea 1: Modelo de Datos y Migraciones
- [ ] Definir modelo `Event` en Prisma schema
- [ ] Definir enums `EventType`, `EventMode`, `EventStatus`
- [ ] Crear migración inicial
- [ ] Crear seeds para testing (5-10 eventos de ejemplo)
- [ ] **Branch**: `feature/spec-01-data-model`

### Tarea 2: Backend - Endpoints CRUD
- [ ] Implementar Zod schemas de validación para Event
- [ ] Implementar `POST /events` (crear evento)
- [ ] Implementar `GET /events` (listar con filtros y paginación)
- [ ] Implementar `GET /events/:id` (obtener detalle)
- [ ] Implementar `PATCH /events/:id` (actualizar)
- [ ] Implementar `DELETE /events/:id` (soft delete)
- [ ] Implementar `PATCH /events/:id/status` (cambiar estado)
- [ ] Implementar middleware de autorización (solo organizador)
- [ ] **Branch**: `feature/spec-01-backend-crud`

### Tarea 3: Backend - Lógica de Negocio
- [ ] Servicio de cálculo de cupo disponible
- [ ] Servicio de validación de fechas
- [ ] Servicio de verificación de inscripción abierta
- [ ] Asignación automática de rol organizador al crear evento
- [ ] Transacciones para operaciones atómicas
- [ ] **Branch**: `feature/spec-01-business-logic`

### Tarea 4: Frontend - Listado de Eventos
- [ ] Crear componente `EventCard`
- [ ] Crear componente `EventList` con grid responsive
- [ ] Implementar filtros (tipo, modo, estado)
- [ ] Implementar buscador con debounce
- [ ] Implementar paginación
- [ ] Conectar con API `GET /events`
- [ ] Implementar skeleton loaders
- [ ] **Branch**: `feature/spec-01-frontend-list`

### Tarea 5: Frontend - Detalle de Evento
- [ ] Crear componente `EventDetail`
- [ ] Mostrar toda la información del evento
- [ ] Mostrar cupo disponible y estado de inscripción
- [ ] Mostrar badge de estado (publicado, en curso, finalizado)
- [ ] Conectar con API `GET /events/:id`
- [ ] **Branch**: `feature/spec-01-frontend-detail`

### Tarea 6: Frontend - Formulario de Evento
- [ ] Crear componente `EventForm` (crear/editar)
- [ ] Validaciones de formulario (fechas, campos requeridos)
- [ ] Integrar con `POST /events` y `PATCH /events/:id`
- [ ] Manejar estados de carga y errores
- [ ] Redirección post-creación al detalle del evento
- [ ] **Branch**: `feature/spec-01-frontend-form`

### Tarea 7: Pruebas
- [ ] Tests unitarios de validación Zod
- [ ] Tests unitarios de servicios de negocio
- [ ] Tests de integración de endpoints CRUD
- [ ] Tests de integración de filtros y paginación
- [ ] Tests E2E de flujo completo (crear -> listar -> ver -> editar -> eliminar)
- [ ] **Branch**: `feature/spec-01-tests`

---

## 7. Estrategia de Verificación

### Pruebas Unitarias
- **Validación de schemas Zod**: Verificar que los datos inválidos son rechazados correctamente
- **Servicio de cálculo de cupo**: Verificar fórmulas con diferentes escenarios (con/sin maxCapacity)
- **Servicio de validación de fechas**: Verificar todas las combinaciones de fechas válidas/inválidas

### Pruebas de Integración
- **Endpoints CRUD**: Verificar que cada endpoint retorna los códigos HTTP correctos
- **Filtros**: Verificar que cada filtro devuelve resultados correctos
- **Paginación**: Verificar que la paginación funciona correctamente con diferentes límites
- **Autorización**: Verificar que endpoints protegidos rechazan requests sin token
- **Soft delete**: Verificar que eventos eliminados no aparecen en listados

### Pruebas E2E (Playwright)
- **Flujo de creación**: Login -> Crear evento -> Ver en listado -> Ver detalle
- **Flujo de edición**: Login -> Editar evento -> Ver cambios reflejados
- **Flujo de filtros**: Aplicar cada filtro y verificar resultados
- **Flujo de eliminación**: Crear -> Eliminar -> Verificar que no aparece
- **Responsive**: Verificar que el listado se adapta a diferentes tamaños de pantalla

### Criterios de Aceptación de la Spec
- [ ] Todos los endpoints de la sección 2 de Contracts.md implementados
- [ ] Cobertura de tests > 80%
- [ ] Todos los tests pasando
- [ ] Frontend funcional con listados, filtros, detalle y formulario
- [ ] Validaciones de negocio funcionando correctamente
- [ ] Documentación de API actualizada
