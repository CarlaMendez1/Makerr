# Contracts.md - Contratos de API

## Base URL
```
/api/v1
```

## Tipos Compartidos

### User
```typescript
interface User {
  id: string;
  firstName: string;
  lastName: string;
  email: string;
  phone?: string;
  institution?: string;
  avatar?: string;
  createdAt: string;
  updatedAt: string;
}
```

### Event
```typescript
interface Event {
  id: string;
  title: string;
  description: string;
  type: 'curso' | 'jornada' | 'congreso' | 'charla' | 'taller' | 'seminario';
  startDate: string;
  endDate: string;
  location: string;
  mode: 'presencial' | 'virtual' | 'hibrido';
  minCapacity?: number;
  maxCapacity?: number;
  registrationStartDate: string;
  registrationEndDate: string;
  organizerId: string;
  status: 'borrador' | 'publicado' | 'en_curso' | 'finalizado' | 'cancelado';
  imageUrl?: string;
  createdAt: string;
  updatedAt: string;
}
```

### Registration
```typescript
interface Registration {
  id: string;
  eventId: string;
  userId: string;
  role: 'participante' | 'disertante' | 'organizador';
  status: 'pendiente' | 'confirmada' | 'cancelada';
  registeredAt: string;
  attended: boolean;
  accredited: boolean;
}
```

### Certificate
```typescript
interface Certificate {
  id: string;
  registrationId: string;
  userId: string;
  eventId: string;
  type: 'asistencia' | 'aprobacion' | 'autor' | 'expositor';
  issuedAt: string;
  verificationCode: string;
  pdfUrl?: string;
}
```

### Survey
```typescript
interface Survey {
  id: string;
  eventId: string;
  title: string;
  description: string;
  isActive: boolean;
  createdAt: string;
  updatedAt: string;
}

interface SurveyQuestion {
  id: string;
  surveyId: string;
  question: string;
  type: 'rating' | 'multiple_choice' | 'text' | 'likert';
  options?: string[];
  required: boolean;
  order: number;
}

interface SurveyResponse {
  id: string;
  surveyId: string;
  registrationId: string;
  userId: string;
  answers: SurveyAnswer[];
  submittedAt: string;
}

interface SurveyAnswer {
  questionId: string;
  value: string | number;
  comment?: string;
}
```

### Comment
```typescript
interface Comment {
  id: string;
  eventId: string;
  userId: string;
  content: string;
  rating?: number;
  createdAt: string;
  updatedAt: string;
}
```

### Role
```typescript
interface UserRole {
  id: string;
  userId: string;
  eventId: string;
  role: 'organizador' | 'participante' | 'disertante';
  assignedAt: string;
  assignedBy: string;
}
```

---

## 1. Autenticación y Usuarios

### 1.1 Registro de Usuario
```
POST /auth/register
```
**Request Body:**
```json
{
  "firstName": "string",
  "lastName": "string",
  "email": "string",
  "password": "string",
  "phone": "string (optional)",
  "institution": "string (optional)"
}
```
**Response (201):**
```json
{
  "success": true,
  "data": {
    "user": { "User" },
    "token": "string"
  }
}
```

### 1.2 Login
```
POST /auth/login
```
**Request Body:**
```json
{
  "email": "string",
  "password": "string"
}
```
**Response (200):**
```json
{
  "success": true,
  "data": {
    "user": { "User" },
    "token": "string"
  }
}
```

### 1.3 Obtener Perfil
```
GET /users/me
```
**Headers:** `Authorization: Bearer <token>`
**Response (200):**
```json
{
  "success": true,
  "data": { "User" }
}
```

### 1.4 Actualizar Perfil
```
PATCH /users/me
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "firstName": "string (optional)",
  "lastName": "string (optional)",
  "phone": "string (optional)",
  "institution": "string (optional)",
  "avatar": "string (optional)"
}
```
**Response (200):**
```json
{
  "success": true,
  "data": { "User" }
}
```

---

## 2. Eventos (SPEC-01)

### 2.1 Listar Eventos Públicos
```
GET /events
```
**Query Params:**
- `type`: string (optional) - Filter by event type
- `status`: string (optional) - `proximo`, `en_curso`, `finalizado`
- `mode`: string (optional) - `presencial`, `virtual`, `hibrido`
- `search`: string (optional) - Search by title/description
- `page`: number (default: 1)
- `limit`: number (default: 20)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "events": [{ "Event" }],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "totalPages": 5
    }
  }
}
```

### 2.2 Obtener Evento por ID
```
GET /events/:id
```
**Response (200):**
```json
{
  "success": true,
  "data": { "Event" }
}
```

### 2.3 Crear Evento
```
POST /events
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "title": "string",
  "description": "string",
  "type": "curso | jornada | congreso | charla | taller | seminario",
  "startDate": "ISO8601",
  "endDate": "ISO8601",
  "location": "string",
  "mode": "presencial | virtual | hibrido",
  "minCapacity": "number (optional)",
  "maxCapacity": "number (optional)",
  "registrationStartDate": "ISO8601",
  "registrationEndDate": "ISO8601",
  "imageUrl": "string (optional)"
}
```
**Response (201):**
```json
{
  "success": true,
  "data": { "Event" }
}
```

### 2.4 Actualizar Evento
```
PATCH /events/:id
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:** Partial of Event creation body
**Response (200):**
```json
{
  "success": true,
  "data": { "Event" }
}
```

### 2.5 Eliminar Evento
```
DELETE /events/:id
```
**Headers:** `Authorization: Bearer <token>`
**Response (204):** No content

### 2.6 Cambiar Estado del Evento
```
PATCH /events/:id/status
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "status": "publicado | en_curso | finalizado | cancelado"
}
```
**Response (200):**
```json
{
  "success": true,
  "data": { "Event" }
}
```

---

## 3. Inscripciones (SPEC-02)

### 3.1 Inscribirse a Evento (Auto-inscripción)
```
POST /events/:eventId/registrations
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "role": "participante | disertante"
}
```
**Response (201):**
```json
{
  "success": true,
  "data": { "Registration" }
}
```

### 3.2 Listar Inscripciones de un Evento
```
GET /events/:eventId/registrations
```
**Headers:** `Authorization: Bearer <token>`
**Query Params:**
- `status`: string (optional)
- `role`: string (optional)
- `page`: number
- `limit`: number

**Response (200):**
```json
{
  "success": true,
  "data": {
    "registrations": [{
      "Registration",
      "user": { "User" }
    }],
    "pagination": { "Pagination" }
  }
}
```

### 3.3 Obtener Inscripción Propia
```
GET /events/:eventId/registrations/me
```
**Headers:** `Authorization: Bearer <token>`
**Response (200):**
```json
{
  "success": true,
  "data": { "Registration" }
}
```

### 3.4 Cancelar Inscripción
```
DELETE /events/:eventId/registrations/me
```
**Headers:** `Authorization: Bearer <token>`
**Response (204):** No content

### 3.5 Inscribir Participante (Staff)
```
POST /events/:eventId/registrations/admin
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "userId": "string",
  "role": "participante | disertante"
}
```
**Response (201):**
```json
{
  "success": true,
  "data": { "Registration" }
}
```

### 3.6 Actualizar Estado de Inscripción
```
PATCH /events/:eventId/registrations/:registrationId
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "status": "confirmada | cancelada",
  "attended": "boolean (optional)"
}
```
**Response (200):**
```json
{
  "success": true,
  "data": { "Registration" }
}
```

---

## 4. Roles y Acreditación (SPEC-03)

### 4.1 Asignar Rol
```
POST /events/:eventId/roles
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "userId": "string",
  "role": "organizador | participante | disertante"
}
```
**Response (201):**
```json
{
  "success": true,
  "data": { "UserRole" }
}
```

### 4.2 Listar Roles de un Evento
```
GET /events/:eventId/roles
```
**Headers:** `Authorization: Bearer <token>`
**Response (200):**
```json
{
  "success": true,
  "data": [{
    "UserRole",
    "user": { "User" }
  }]
}
```

### 4.3 Revocar Rol
```
DELETE /events/:eventId/roles/:roleId
```
**Headers:** `Authorization: Bearer <token>`
**Response (204):** No content

### 4.4 Acreditar Participante
```
POST /events/:eventId/accreditations
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "userId": "string",
  "accreditationCode": "string (optional)"
}
```
**Response (201):**
```json
{
  "success": true,
  "data": {
    "registration": { "Registration" },
    "accreditationCode": "string"
  }
}
```

### 4.5 Verificar Acreditación
```
GET /accreditations/verify/:code
```
**Response (200):**
```json
{
  "success": true,
  "data": {
    "valid": true,
    "registration": { "Registration" },
    "event": { "Event" },
    "user": { "User" }
  }
}
```

### 4.6 Generar Código QR de Acreditación
```
GET /events/:eventId/accreditations/:userId/qr
```
**Headers:** `Authorization: Bearer <token>`
**Response (200):**
```json
{
  "success": true,
  "data": {
    "qrCodeBase64": "string",
    "accreditationCode": "string"
  }
}
```

---

## 5. Certificados y Encuestas (SPEC-04)

### 5.1 Generar Certificado
```
POST /certificates
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "registrationId": "string",
  "type": "asistencia | aprobacion | autor | expositor"
}
```
**Response (201):**
```json
{
  "success": true,
  "data": { "Certificate" }
}
```

### 5.2 Listar Certificados de Usuario
```
GET /certificates
```
**Headers:** `Authorization: Bearer <token>`
**Query Params:**
- `eventId`: string (optional)
- `type`: string (optional)

**Response (200):**
```json
{
  "success": true,
  "data": [{
    "Certificate",
    "event": { "Event" }
  }]
}
```

### 5.3 Descargar Certificado PDF
```
GET /certificates/:id/download
```
**Headers:** `Authorization: Bearer <token>`
**Response (200):**
- Content-Type: `application/pdf`
- Body: PDF file stream

### 5.4 Verificar Certificado
```
GET /certificates/verify/:verificationCode
```
**Response (200):**
```json
{
  "success": true,
  "data": {
    "valid": true,
    "certificate": { "Certificate" },
    "event": { "Event" },
    "user": { "User" }
  }
}
```

### 5.5 Crear Encuesta
```
POST /events/:eventId/surveys
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "title": "string",
  "description": "string",
  "questions": [{
    "question": "string",
    "type": "rating | multiple_choice | text | likert",
    "options": ["string"] (optional),
    "required": true,
    "order": 1
  }]
}
```
**Response (201):**
```json
{
  "success": true,
  "data": {
    "survey": { "Survey" },
    "questions": [{ "SurveyQuestion" }]
  }
}
```

### 5.6 Obtener Encuesta Activa de Evento
```
GET /events/:eventId/survey
```
**Response (200):**
```json
{
  "success": true,
  "data": {
    "survey": { "Survey" },
    "questions": [{ "SurveyQuestion" }]
  }
}
```

### 5.7 Enviar Respuesta de Encuesta
```
POST /events/:eventId/survey/responses
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "answers": [{
    "questionId": "string",
    "value": "string | number",
    "comment": "string (optional)"
  }]
}
```
**Response (201):**
```json
{
  "success": true,
  "data": { "SurveyResponse" }
}
```

### 5.8 Crear Comentario
```
POST /events/:eventId/comments
```
**Headers:** `Authorization: Bearer <token>`
**Request Body:**
```json
{
  "content": "string",
  "rating": "number (1-5, optional)"
}
```
**Response (201):**
```json
{
  "success": true,
  "data": { "Comment" }
}
```

### 5.9 Listar Comentarios de Evento
```
GET /events/:eventId/comments
```
**Query Params:**
- `page`: number
- `limit`: number
- `sortBy`: string (recent, rating)

**Response (200):**
```json
{
  "success": true,
  "data": {
    "comments": [{
      "Comment",
      "user": {
        "firstName": "string",
        "lastName": "string",
        "avatar": "string"
      }
    }],
    "pagination": { "Pagination" }
  }
}
```

### 5.10 Generar Informe de Evento
```
GET /events/:eventId/report
```
**Headers:** `Authorization: Bearer <token>`
**Query Params:**
- `type`: string - `general`, `attendance`, `satisfaction`, `agenda`

**Response (200):**
```json
{
  "success": true,
  "data": {
    "reportType": "string",
    "generatedAt": "ISO8601",
    "content": {
      "summary": {},
      "statistics": {},
      "agenda": []
    }
  }
}
```

---

## 6. Respuestas de Error

### 400 Bad Request
```json
{
  "success": false,
  "message": "Datos de entrada inválidos",
  "errors": [
    {
      "field": "email",
      "message": "El email no es válido"
    }
  ]
}
```

### 401 Unauthorized
```json
{
  "success": false,
  "message": "No autenticado. Token requerido."
}
```

### 403 Forbidden
```json
{
  "success": false,
  "message": "No tienes permisos para realizar esta acción"
}
```

### 404 Not Found
```json
{
  "success": false,
  "message": "Recurso no encontrado"
}
```

### 409 Conflict
```json
{
  "success": false,
  "message": "El evento no permite inscripciones en este momento"
}
```

### 500 Internal Server Error
```json
{
  "success": false,
  "message": "Error interno del servidor"
}
```
