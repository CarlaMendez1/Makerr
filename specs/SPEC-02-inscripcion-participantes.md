# SPEC-02: Inscripción de Participantes

## 1. Objetivo y Contexto

### Objetivo
Implementar el sistema de inscripción de participantes a eventos académicos, permitiendo tanto la auto-inscripción por parte de usuarios registrados como la inscripción administrativa por parte del personal del evento (organizadores).

### Contexto
Una vez que los eventos están creados y publicados (SPEC-01), los usuarios necesitan poder inscribirse para participar. El sistema debe soportar:
- Inscripción autónoma: el usuario se inscribe directamente desde la interfaz
- Inscripción administrativa: el organizador inscribe participantes manualmente
- Control de cupos: respetar límites mínimos y máximos
- Períodos de inscripción: solo permitir inscripciones dentro de las fechas configuradas
- Gestión de estados: pendiente, confirmada, cancelada

### Alcance
- Auto-inscripción a eventos desde la vista de detalle
- Inscripción administrativa por organizadores
- Cancelación de inscripción por el participante
- Listado de inscripciones por evento (para organizadores)
- Validación de cupos y fechas
- Gestión de estados de inscripción

---

## 2. Historias de Usuario y Criterios de Aceptación

### HU-01: Auto-inscripción a Evento
**Como** usuario registrado  
**Quiero** inscribirme a un evento publicado de forma segura 
**Para** participar como asistente sin que mi identidad sea manipulada ni se alteren los cupos del evento de forma fraudulenta.

**Criterios de Aceptación:**
- Dado que estoy autenticado y veo un evento publicado, entonces veo un botón "Inscribirme" si la inscripción está abierta, y toda petición enviada al backend requerirá   validación estricta de origen para prevenir ataques de falsificación de peticiones (CSRF).
- Dado que me inscribo al evento, entonces el backend validará que el ID de usuario a inscribir coincida estrictamente con la identidad validada en el token de sesión (JWT), evitando vulnerabilidades de Control de Acceso Roto (OWASP A01:2021) donde un atacante podría inscribir a terceros manipulando la API.
- Dado que el evento alcanzó su cupo máximo, entonces veo un mensaje "Cupo lleno" y no puedo inscribirme. El sistema debe implementar una transacción atómica al momento de registrar la inscripción para evitar "condiciones de carrera" (Race Conditions) si múltiples usuarios intentan acceder al último cupo simultáneamente (Control Arquitectónico de Performance/Seguridad).
- Dado que la inscripción está cerrada (fuera de fechas), entonces veo un mensaje "Inscripción cerrada" en lugar del botón, y cualquier intento directo de forzar el registro mediante llamadas directas a la API será interceptado y rechazado de forma segura (OWASP C3: Validación de entradas).
- Dado que me inscribo exitosamente, entonces recibo una confirmación y mi estado es "pendiente".

### HU-02: Cancelar Inscripción
**Como** participante inscrito  
**Quiero** cancelar mi inscripción a un evento  
**Para** liberar mi lugar si no puedo asistir  

**Criterios de Aceptación:**
- Dado que estoy inscrito a un evento, entonces veo un botón "Cancelar inscripción"
- Dado que cancelo mi inscripción, entonces mi estado cambia a "cancelada"
- Dado que cancelo mi inscripción, entonces el cupo disponible incrementa
- Dado que el evento ya comenzó (en_curso), entonces no puedo cancelar mi inscripción
- Dado que el evento ya finalizó, entonces no puedo cancelar mi inscripción

### HU-03: Inscribir Participante (Staff)
**Como** organizador de un evento  
**Quiero** inscribir a un usuario desde el panel de administración  
**Para** registrar participantes que no tienen acceso a la plataforma  

**Criterios de Aceptación:**
- Dado que soy organizador, entonces puedo acceder a un panel de inscripciones
- Dado que busco un usuario por email, entonces puedo seleccionarlo para inscribirlo
- Dado que inscribo un usuario, entonces puedo asignarle un rol (participante/disertante)
- Dado que el usuario ya está inscrito, entonces veo un mensaje de error y no se duplica
- Dado que el evento tiene cupo máximo y está lleno, entonces no puedo inscribir más participantes
- Dado que inscribo exitosamente, entonces la inscripción queda en estado "confirmada" automáticamente

### HU-04: Gestionar Inscripciones del Evento
**Como** organizador de un evento  
**Quiero** ver y gestionar todas las inscripciones de mi evento  
**Para** controlar quiénes participarán  

**Criterios de Aceptación:**
- Dado que accedo al panel de inscripciones, entonces veo una tabla con todas las inscripciones
- Dado que hay muchas inscripciones, entonces veo paginación y filtros por estado y rol
- Dado que una inscripción está pendiente, entonces puedo confirmarla o rechazarla
- Dado que confirmo una inscripción, entonces el estado cambia a "confirmada"
- Dado que rechazo una inscripción, entonces el estado cambia a "cancelada" y se libera el cupo
- Dado que marco un participante como "asistió", entonces el campo attended se actualiza

### HU-05: Ver Mis Inscripciones
**Como** usuario registrado  
**Quiero** ver todos los eventos a los que estoy inscrito  
**Para** gestionar mi participación  

**Criterios de Aceptación:**
- Dado que accedo a "Mis inscripciones", entonces veo una lista de todos mis eventos
- Dado que tengo inscripciones en diferentes estados, entonces veo badges de estado (pendiente, confirmada, cancelada)
- Dado que un evento es próximo, entonces veo un botón para ver el detalle
- Dado que un evento finalizó, entonces veo un botón para descargar certificado (si aplica)

### HU-06: Notificación de Inscripción
**Como** usuario inscrito  
**Quiero** recibir confirmación de mi inscripción  
**Para** tener constancia de mi registro  

**Criterios de Aceptación:**
- Dado que me inscribo exitosamente, entonces veo un mensaje de confirmación en pantalla
- Dado que me inscribo, entonces mi inscripción aparece en "Mis inscripciones"
- Dado que mi inscripción es confirmada por el organizador, entonces veo el estado actualizado
- Dado que mi inscripción es rechazada, entonces veo el motivo si el organizador lo proporciona

---

## 3. Requisitos Funcionales y Reglas de Negocio

### Requisitos Funcionales
- **RF-01**: El sistema debe permitir que un usuario autenticado se inscriba a un evento publicado
- **RF-02**: El sistema debe validar que la inscripción se realiza dentro del período de inscripción
- **RF-03**: El sistema debe validar que no se exceda el cupo máximo del evento
- **RF-04**: El sistema debe permitir cancelar una inscripción antes del inicio del evento
- **RF-05**: El sistema debe permitir a los organizadores inscribir usuarios manualmente
- **RF-06**: El sistema debe permitir a los organizadores confirmar o rechazar inscripciones pendientes
- **RF-07**: El sistema debe permitir marcar la asistencia de participantes
- **RF-08**: El sistema debe mostrar al usuario sus inscripciones activas
- **RF-09**: El sistema debe prevenir inscripciones duplicadas al mismo evento
- **RF-10**: El sistema debe calcular el cupo disponible en tiempo real

### Reglas de Negocio
- **RN-01**: Solo usuarios autenticados pueden inscribirse a eventos
- **RN-02**: Solo se puede inscribir a eventos en estado "publicado" o "en_curso"
- **RN-03**: La inscripción solo está permitida entre registrationStartDate y registrationEndDate
- **RN-04**: Si maxCapacity está definido y se alcanza, no se permiten más inscripciones
- **RN-05**: Si minCapacity no se alcanza antes del evento, el organizador puede cancelar el evento
- **RN-06**: Un usuario solo puede tener una inscripción activa por evento
- **RN-07**: Las inscripciones creadas por el organizador se crean como "confirmadas" directamente
- **RN-08**: Las auto-inscripciones se crean como "pendientes" si el evento requiere confirmación
- **RN-09**: No se puede cancelar una inscripción después del inicio del evento
- **RN-10**: Solo el organizador puede modificar el estado de una inscripción de otro usuario
- **RN-11**: Al cancelar una inscripción, se libera el cupo inmediatamente
- **RN-12**: El campo "attended" solo puede ser establecido por el organizador

---

## 4. Restricciones Técnicas Específicas

### Backend
- Implementar endpoints RESTful según Contracts.md sección 3
- Usar transacciones de Prisma para operaciones que modifican cupo + inscripción
- Implementar validación de cupo con locking optimista (optimistic concurrency)
- Validar período de inscripción en middleware antes de procesar
- Implementar endpoint `GET /events/:eventId/registrations` con autorización de organizador
- Retornar 409 Conflict cuando hay violación de reglas de negocio (cupos, fechas)

### Frontend
- Crear componente `RegistrationButton` con estados (inscribirse, ya inscrito, cupo lleno, cerrado)
- Crear componente `RegistrationList` para panel de organizador con tabla y filtros
- Crear componente `MyRegistrations` para vista de usuario con tarjetas
- Implementar modal de confirmación para cancelar inscripción
- Implementar acciones inline (confirmar/rechazar) en tabla de organizador
- Mostrar toast notifications para feedback de acciones
- Actualizar UI optimista en acciones de inscripción

### Base de Datos
- Tabla `registrations` con índice compuesto único: `[eventId, userId]`
- Índices en: `status`, `role`, `eventId`
- Relación con `events` y `users` con cascade delete apropiado

### Performance
- Validación de cupo: usar consulta atómica para evitar race conditions
- Listado de inscripciones del evento: paginar con 50 registros por página
- Cache del conteo de inscripciones por evento (invalidar al crear/cancelar)

---

## 5. Modelo de Datos

```prisma
model Registration {
  id            String           @id @default(uuid())
  eventId       String
  userId        String
  role          RegistrationRole @default(participante)
  status        RegistrationStatus @default(pendiente)
  registeredAt  DateTime         @default(now())
  attended      Boolean          @default(false)
  accredited    Boolean          @default(false)
  notes         String?
  createdAt     DateTime         @default(now())
  updatedAt     DateTime         @updatedAt

  event         Event            @relation(fields: [eventId], references: [id], onDelete: Cascade)
  user          User             @relation(fields: [userId], references: [id], onDelete: Cascade)
  certificates  Certificate[]
  surveyResponses SurveyResponse[]

  @@unique([eventId, userId])
  @@index([eventId])
  @@index([userId])
  @@index([status])
  @@index([role])
  @@map("registrations")
}

enum RegistrationRole {
  participante
  disertante
  organizador
}

enum RegistrationStatus {
  pendiente
  confirmada
  cancelada
}
```

### Modelo de Respuesta API (TypeScript)

```typescript
interface RegistrationResponse {
  id: string;
  eventId: string;
  userId: string;
  role: RegistrationRole;
  status: RegistrationStatus;
  registeredAt: string;
  attended: boolean;
  accredited: boolean;
  notes?: string;
  user?: {
    id: string;
    firstName: string;
    lastName: string;
    email: string;
    institution?: string;
  };
  event?: {
    id: string;
    title: string;
    startDate: string;
  };
}

interface RegistrationListResponse {
  registrations: RegistrationResponse[];
  pagination: {
    page: number;
    limit: number;
    total: number;
    totalPages: number;
  };
  summary: {
    total: number;
    confirmed: number;
    pending: number;
    cancelled: number;
  };
}

interface RegistrationCreationRequest {
  role: 'participante' | 'disertante';
}

interface AdminRegistrationRequest {
  userId: string;
  role: 'participante' | 'disertante';
}
```

---

## 6. Plan de Tareas

### Tarea 1: Modelo de Datos y Migraciones
- [ ] Definir modelo `Registration` en Prisma schema
- [ ] Definir enums `RegistrationRole`, `RegistrationStatus`
- [ ] Agregar relación entre User y Registration
- [ ] Crear migración
- [ ] Crear seeds con inscripciones de ejemplo
- [ ] **Branch**: `feature/spec-02-data-model`

### Tarea 2: Backend - Auto-inscripción
- [ ] Implementar Zod schema para Registration
- [ ] Implementar `POST /events/:eventId/registrations` (auto-inscripción)
- [ ] Validar período de inscripción
- [ ] Validar cupo máximo
- [ ] Prevenir inscripciones duplicadas
- [ ] Implementar `GET /events/:eventId/registrations/me` (mi inscripción)
- [ ] Implementar `DELETE /events/:eventId/registrations/me` (cancelar inscripción)
- [ ] **Branch**: `feature/spec-02-backend-self-registration`

### Tarea 3: Backend - Gestión Administrativa
- [ ] Implementar `POST /events/:eventId/registrations/admin` (inscripción por staff)
- [ ] Implementar `GET /events/:eventId/registrations` (listado con filtros)
- [ ] Implementar `PATCH /events/:eventId/registrations/:registrationId` (actualizar estado)
- [ ] Middleware de autorización: solo organizador del evento
- [ ] Implementar conteo de cupo disponible con transacción
- [ ] Implementar resumen de inscripciones (total, confirmadas, pendientes)
- [ ] **Branch**: `feature/spec-02-backend-admin`

### Tarea 4: Frontend - Auto-inscripción
- [ ] Crear componente `RegistrationButton` con estados dinámicos
- [ ] Integrar botón en `EventDetail` (SPEC-01)
- [ ] Implementar modal de confirmación de inscripción
- [ ] Mostrar feedback visual (toast) al inscribirse
- [ ] Actualizar UI optimista tras inscripción
- [ ] **Branch**: `feature/spec-02-frontend-self-registration`

### Tarea 5: Frontend - Panel de Organizador
- [ ] Crear componente `RegistrationList` con tabla
- [ ] Implementar filtros por estado y rol
- [ ] Implementar paginación
- [ ] Acciones inline: confirmar, rechazar, marcar asistencia
- [ ] Mostrar resumen de inscripciones (cards con contadores)
- [ ] Conectar con endpoints de administración
- [ ] **Branch**: `feature/spec-02-frontend-admin`

### Tarea 6: Frontend - Mis Inscripciones
- [ ] Crear página "Mis Inscripciones"
- [ ] Crear componente `MyRegistrations` con tarjetas
- [ ] Mostrar estado con badges de color
- [ ] Botón de cancelar inscripción con modal de confirmación
- [ ] Links a detalle de evento
- [ ] **Branch**: `feature/spec-02-frontend-my-registrations`

### Tarea 7: Pruebas
- [ ] Tests unitarios de validación de cupo y fechas
- [ ] Tests de integración de endpoints de inscripción
- [ ] Tests de concurrencia (múltiples inscripciones simultáneas al cupo)
- [ ] Tests E2E de flujo completo de inscripción
- [ ] Tests E2E de cancelación de inscripción
- [ ] Tests E2E de panel de organizador
- [ ] **Branch**: `feature/spec-02-tests`

---

## 7. Estrategia de Verificación

### Pruebas Unitarias
- **Validación de fechas de inscripción**: Verificar inscripciones dentro/fuera de período
- **Cálculo de cupo**: Verificar con diferentes configuraciones (con/sin límites)
- **Prevención de duplicados**: Verificar que no se permite doble inscripción

### Pruebas de Integración
- **Auto-inscripción**: Verificar creación exitosa, validaciones, y respuestas HTTP
- **Inscripción admin**: Verificar que solo organizadores pueden acceder
- **Cancelación**: Verificar que se libera el cupo correctamente
- **Actualización de estado**: Verificar transiciones de estado válidas/inválidas
- **Listado con filtros**: Verificar que cada filtro retorna resultados correctos

### Pruebas de Concurrencia
- **Race condition de cupo**: Simular 10 inscripciones simultáneas cuando quedan 5 lugares
- Verificar que exactamente 5 se confirman y 5 reciben error 409

### Pruebas E2E (Playwright)
- **Flujo de auto-inscripción**: Login -> Ver evento -> Inscribirse -> Ver confirmación -> Ver en "Mis inscripciones"
- **Flujo de cancelación**: Login -> Mis inscripciones -> Cancelar -> Ver que se liberó cupo
- **Flujo de gestión admin**: Login como organizador -> Ver inscripciones -> Confirmar pendiente -> Marcar asistencia
- **Flujo de cupo lleno**: Intentar inscribirse cuando no hay cupo -> Ver mensaje de error
- **Flujo de inscripción fuera de fecha**: Intentar inscribirse fuera del período -> Ver mensaje de error

### Criterios de Aceptación de la Spec
- [ ] Todos los endpoints de la sección 3 de Contracts.md implementados
- [ ] Cobertura de tests > 80%
- [ ] Todos los tests pasando
- [ ] Frontend de inscripción funcional desde vista de detalle
- [ ] Panel de administrador funcional con gestión de inscripciones
- [ ] Vista "Mis inscripciones" funcional
- [ ] Validaciones de cupo y fechas funcionando correctamente
- [ ] No hay race conditions en la gestión de cupos
