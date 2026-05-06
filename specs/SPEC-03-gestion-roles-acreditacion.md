# SPEC-03: Gestión de Roles y Acreditación

## 1. Objetivo y Contexto

### Objetivo
Implementar el sistema de gestión de roles dentro de los eventos académicos y el módulo de acreditación de participantes, permitiendo asignar roles específicos (organizador, participante, disertante) y generar códigos de acreditación verificables con soporte de QR.

### Contexto
En un evento académico, los participantes tienen diferentes roles que determinan sus permisos y responsabilidades. Además, es necesario un sistema de acreditación que permita verificar la identidad y el derecho a asistir al evento, especialmente en eventos presenciales donde se requiere control de acceso.

El sistema de roles determina:
- **Organizador**: Puede gestionar el evento, inscripciones, roles y acreditaciones
- **Disertante**: Puede acceder a materiales del evento y tiene un rol destacado
- **Participante**: Rol estándar con acceso básico al evento

El sistema de acreditación permite:
- Generar códigos únicos por participante
- Verificar la acreditación en la entrada del evento
- Generar códigos QR para escaneo rápido
- Registrar la acreditación como constancia

### Alcance
- CRUD de roles por evento
- Asignación y revocación de roles
- Generación de códigos de acreditación
- Verificación de acreditaciones
- Generación de códigos QR
- Panel de acreditación para staff del evento

---

## 2. Historias de Usuario y Criterios de Aceptación

### HU-01: Asignar Rol a Usuario
**Como** organizador de un evento  
**Quiero** asignar un rol a un usuario dentro del evento  
**Para** definir sus permisos y responsabilidades  

**Criterios de Aceptación:**
- Dado que soy organizador, entonces puedo asignar roles a otros usuarios del evento
- Dado que asigno un rol, entonces el usuario recibe los permisos correspondientes
- Dado que el rol es "organizador", entonces el usuario puede gestionar el evento
- Dado que el rol es "disertante", entonces el usuario aparece destacado en el programa
- Dado que el usuario ya tiene ese rol asignado, entonces veo un mensaje de error
- Dado que asigno un rol exitosamente, entonces queda registrado quién y cuándo lo asignó

### HU-02: Revocar Rol
**Como** organizador de un evento  
**Quierto** revocar el rol de un usuario  
**Para** retirar permisos cuando ya no son necesarios  

**Criterios de Aceptación:**
- Dado que soy organizador, entonces puedo revocar roles asignados a otros usuarios
- Dado que revoco un rol, entonces el usuario pierde los permisos asociados
- Dado que intento revocar el rol de organizador principal, entonces no puedo (debe haber al menos uno)
- Dado que revoco un rol exitosamente, entonces la acción queda registrada

### HU-03: Ver Roles del Evento
**Como** organizador de un evento  
**Quiero** ver todos los roles asignados en mi evento  
**Para** tener control de quién tiene qué permisos  

**Criterios de Aceptación:**
- Dado que accedo a la gestión de roles, entonces veo una lista de todos los usuarios con sus roles
- Dado que hay muchos usuarios, entonces veo la lista paginada
- Dado que aplico un filtro por rol, entonces solo veo usuarios con ese rol
- Dado que busco un usuario por nombre, entonces puedo encontrarlo rápidamente

### HU-04: Generar Acreditación
**Como** organizador de un evento  
**Quiero** acreditar a un participante inscrito  
**Para** validar su derecho a asistir al evento  

**Criterios de Aceptación:**
- Dado que un usuario tiene inscripción confirmada, entonces puedo acreditarlo
- Dado que acredito a un participante, entonces se genera un código único de acreditación
- Dado que acredito exitosamente, entonces el campo `accredited` de la inscripción se marca como true
- Dado que el participante ya está acreditado, entonces veo su acreditación existente
- Dado que genero acreditación, entonces el sistema genera un código alfanumérico de 12 caracteres

### HU-05: Verificar Acreditación
**Como** staff del evento  
**Quiero** verificar el código de acreditación de una persona  
**Para** permitirle o denegarle el acceso al evento  

**Criterios de Aceptación:**
- Dado que ingreso un código de acreditación válido, entonces veo los datos del participante y su evento
- Dado que el código es válido y corresponde al evento actual, entonces veo un indicador verde de acceso permitido
- Dado que el código es válido pero corresponde a otro evento, entonces veo un indicador de evento incorrecto
- Dado que el código no existe, entonces veo un indicador rojo de código inválido
- Dado que verifico un código, entonces queda registrado el timestamp de verificación

### HU-06: Generar Código QR de Acreditación
**Como** participante acreditado  
**Quiero** obtener mi código QR de acreditación  
**Para** presentarlo en la entrada del evento  

**Criterios de Aceptación:**
- Dado que estoy acreditado en un evento, entonces puedo ver mi código QR desde "Mis inscripciones"
- Dado que veo mi QR, entonces contiene mi código de acreditación codificado
- Dado que descargo mi QR, entonces se genera una imagen PNG descargable
- Dado que no estoy acreditado, entonces no veo la opción de QR

### HU-07: Panel de Acreditación
**Como** staff del evento  
**Quiero** un panel para gestionar acreditaciones del evento  
**Para** controlar el acceso de manera eficiente  

**Criterios de Aceptación:**
- Dado que accedo al panel de acreditación, entonces veo las estadísticas del evento
- Dado que busco un participante por nombre o código, entonces puedo encontrar su acreditación
- Dado que veo el listado de participantes, entonces veo quiénes están acreditados y quiénes no
- Dado que filtro por "no acreditados", entonces puedo identificar a quién falta acreditar
- Dado que veo el historial de verificaciones, entonces puedo auditar los accesos

---

## 3. Requisitos Funcionales y Reglas de Negocio

### Requisitos Funcionales
- **RF-01**: El sistema debe permitir asignar roles (organizador, participante, disertante) a usuarios dentro de un evento
- **RF-02**: El sistema debe permitir revocar roles asignados
- **RF-03**: El sistema debe listar todos los roles asignados a un evento con información del usuario
- **RF-04**: El sistema debe generar códigos de acreditación únicos al acreditar un participante
- **RF-05**: El sistema debe permitir verificar códigos de acreditación
- **RF-06**: El sistema debe generar códigos QR codificando el código de acreditación
- **RF-07**: El sistema debe registrar el historial de verificaciones de acreditación
- **RF-08**: El sistema debe mostrar estadísticas de acreditación por evento
- **RF-09**: El sistema debe validar que solo un participante con inscripción confirmada pueda ser acreditado
- **RF-10**: El sistema debe mantener al menos un organizador principal por evento

### Reglas de Negocio
- **RN-01**: Solo un organizador puede asignar o revocar roles en su evento
- **RN-02**: Un evento debe tener al menos un organizador en todo momento
- **RN-03**: Un usuario puede tener múltiples roles en diferentes eventos, pero un solo rol por evento
- **RN-04**: Solo participantes con inscripción en estado "confirmada" pueden ser acreditados
- **RN-05**: El código de acreditación debe ser único a nivel global (12 caracteres alfanuméricos)
- **RN-06**: Una vez acreditado, el participante no puede ser desacreditado (solo se puede cancelar inscripción)
- **RN-07**: El código QR debe contener el código de acreditación en formato de texto plano
- **RN-08**: Las verificaciones de acreditación deben registrarse con timestamp
- **RN-09**: Un disertante tiene visibilidad destacada en la agenda del evento
- **RN-10**: El organizador principal es quien creó el evento y no puede ser revocado por otros organizadores

---

## 4. Restricciones Técnicas Específicas

### Backend
- Implementar endpoints RESTful según Contracts.md sección 4
- Generar códigos de acreditación con `crypto.randomBytes` y formato Base32
- Implementar generación de QR con librería `qrcode` en el backend
- Usar transacciones al acreditar (actualizar registration + crear registro)
- Indexar código de acreditación para búsqueda rápida
- Implementar historial de verificaciones en tabla separada

### Frontend
- Crear componente `RoleManager` con tabla de roles y acciones
- Crear componente `AccreditationPanel` para staff del evento
- Crear componente `QRCodeDisplay` para mostrar QR del participante
- Crear componente `VerificationScanner` para escanear/ingresar códigos
- Implementar búsqueda en tiempo real de participantes
- Mostrar estadísticas con gráficos simples (barras)
- Usar librería `qrcode.react` para generación de QR en frontend

### Base de Datos
- Tabla `user_roles` con índice compuesto único: `[userId, eventId]`
- Tabla `accreditation_logs` para historial de verificaciones
- Agregar campo `accreditationCode` en Registration con índice único
- Trigger para generar código al acreditar

### Seguridad
- Códigos de acreditación deben ser criptográficamente seguros
- Endpoint de verificación público pero con rate limiting
- Solo staff autorizado puede acceder al panel de acreditación

---

## 5. Modelo de Datos

```prisma
model UserRole {
  id         String     @id @default(uuid())
  userId     String
  eventId    String
  role       EventRole
  assignedAt DateTime   @default(now())
  assignedBy String

  user       User       @relation(fields: [userId], references: [id], onDelete: Cascade)
  event      Event      @relation(fields: [eventId], references: [id], onDelete: Cascade)
  assigner   User       @relation("RoleAssigner", fields: [assignedBy], references: [id])

  @@unique([userId, eventId])
  @@index([eventId])
  @@index([userId])
  @@index([role])
  @@map("user_roles")
}

model AccreditationLog {
  id              String     @id @default(uuid())
  registrationId  String
  eventId         String
  verifiedAt      DateTime   @default(now())
  verifiedBy      String?
  valid           Boolean
  notes           String?

  registration    Registration @relation(fields: [registrationId], references: [id])
  event           Event      @relation(fields: [eventId], references: [id])

  @@index([registrationId])
  @@index([eventId])
  @@index([verifiedAt])
  @@map("accreditation_logs")
}

enum EventRole {
  organizador
  participante
  disertante
}
```

### Extensión del modelo Registration

```prisma
// Campo adicional en Registration
accreditationCode String? @unique
```

### Modelo de Respuesta API (TypeScript)

```typescript
interface UserRoleResponse {
  id: string;
  userId: string;
  eventId: string;
  role: EventRole;
  assignedAt: string;
  assignedBy: string;
  user: {
    id: string;
    firstName: string;
    lastName: string;
    email: string;
  };
}

interface AccreditationResponse {
  registration: {
    id: string;
    userId: string;
    eventId: string;
    accredited: boolean;
    accreditationCode?: string;
    status: RegistrationStatus;
  };
  accreditationCode: string;
}

interface VerificationResponse {
  valid: boolean;
  registration?: {
    id: string;
    status: RegistrationStatus;
    attended: boolean;
    accredited: boolean;
  };
  event?: {
    id: string;
    title: string;
    startDate: string;
    location: string;
  };
  user?: {
    id: string;
    firstName: string;
    lastName: string;
    email: string;
  };
}

interface QRCodeResponse {
  qrCodeBase64: string;
  accreditationCode: string;
}

interface AccreditationStats {
  totalRegistrations: number;
  accredited: number;
  notAccredited: number;
  attended: number;
  byRole: {
    organizador: number;
    disertante: number;
    participante: number;
  };
}
```

---

## 6. Plan de Tareas

### Tarea 1: Modelo de Datos y Migraciones
- [ ] Definir modelo `UserRole` en Prisma schema
- [ ] Definir modelo `AccreditationLog`
- [ ] Agregar campo `accreditationCode` a Registration
- [ ] Definir enum `EventRole`
- [ ] Crear migración
- [ ] Crear seeds con roles y acreditaciones de ejemplo
- [ ] **Branch**: `feature/spec-03-data-model`

### Tarea 2: Backend - Gestión de Roles
- [ ] Implementar Zod schema para UserRole
- [ ] Implementar `POST /events/:eventId/roles` (asignar rol)
- [ ] Implementar `GET /events/:eventId/roles` (listar roles)
- [ ] Implementar `DELETE /events/:eventId/roles/:roleId` (revocar rol)
- [ ] Validar que siempre quede al menos un organizador
- [ ] Implementar middleware de autorización para gestión de roles
- [ ] **Branch**: `feature/spec-03-backend-roles`

### Tarea 3: Backend - Acreditación
- [ ] Implementar servicio de generación de códigos de acreditación
- [ ] Implementar `POST /events/:eventId/accreditations` (acreditar)
- [ ] Implementar `GET /accreditations/verify/:code` (verificar - público)
- [ ] Implementar `GET /events/:eventId/accreditations/:userId/qr` (generar QR)
- [ ] Registrar logs de verificación en `AccreditationLog`
- [ ] Implementar estadísticas de acreditación
- [ ] **Branch**: `feature/spec-03-backend-accreditation`

### Tarea 4: Frontend - Gestión de Roles
- [ ] Crear componente `RoleManager` con tabla
- [ ] Implementar modal para asignar rol (buscar usuario por email)
- [ ] Acciones de revocar rol con confirmación
- [ ] Filtros por tipo de rol
- [ ] Indicador visual del organizador principal
- [ ] Conectar con endpoints de roles
- [ ] **Branch**: `feature/spec-03-frontend-roles`

### Tarea 5: Frontend - Panel de Acreditación
- [ ] Crear componente `AccreditationPanel`
- [ ] Mostrar estadísticas con tarjetas resumen
- [ ] Implementar búsqueda de participantes
- [ ] Botón de acreditar por participante
- [ ] Indicadores visuales de estado (acreditado/no acreditado)
- [ ] Historial de verificaciones recientes
- [ ] **Branch**: `feature/spec-03-frontend-accreditation`

### Tarea 6: Frontend - QR y Verificación
- [ ] Crear componente `QRCodeDisplay` para participantes
- [ ] Integrar QR en vista de detalle de inscripción
- [ ] Crear componente `VerificationInput` para staff
- [ ] Implementar búsqueda manual de código
- [ ] Mostrar resultado de verificación con indicadores visuales
- [ ] **Branch**: `feature/spec-03-frontend-qr`

### Tarea 7: Pruebas
- [ ] Tests unitarios de generación de códigos
- [ ] Tests unitarios de validación de roles
- [ ] Tests de integración de endpoints de roles
- [ ] Tests de integración de endpoints de acreditación
- [ ] Tests de verificación con códigos válidos/inválidos
- [ ] Tests E2E de flujo completo de roles y acreditación
- [ ] **Branch**: `feature/spec-03-tests`

---

## 7. Estrategia de Verificación

### Pruebas Unitarias
- **Generación de códigos**: Verificar formato (12 caracteres, alfanuméricos, únicos)
- **Validación de roles**: Verificar restricciones de asignación y revocación
- **Regla de organizador mínimo**: Verificar que no se puede revocar al último organizador

### Pruebas de Integración
- **Asignación de rol**: Verificar creación exitosa, duplicados, permisos
- **Revocación de rol**: Verificar eliminación y restricciones
- **Acreditación**: Verificar generación de código y actualización de registration
- **Verificación**: Verificar respuestas con códigos válidos, inválidos y de otros eventos
- **QR**: Verificar generación de imagen base64 válida

### Pruebas E2E (Playwright)
- **Flujo de asignación de rol**: Login como organizador -> Asignar rol -> Ver en lista
- **Flujo de acreditación**: Login como organizador -> Acreditar participante -> Ver código generado
- **Flujo de verificación**: Ingresar código -> Ver resultado -> Ver log registrado
- **Flujo de QR**: Login como participante -> Ver QR -> Descargar imagen
- **Panel de acreditación**: Ver estadísticas -> Buscar participante -> Acreditar

### Criterios de Aceptación de la Spec
- [ ] Todos los endpoints de la sección 4 de Contracts.md implementados
- [ ] Cobertura de tests > 80%
- [ ] Todos los tests pasando
- [ ] Gestión de roles funcional para organizadores
- [ ] Acreditación y verificación funcionando correctamente
- [ ] Códigos QR generados y verificables
- [ ] Panel de acreditación con estadísticas
- [ ] Reglas de negocio validadas (mínimo un organizador, solo confirmados acreditados)
