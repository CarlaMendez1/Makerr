# SPEC-04: Certificados y Encuestas Post-Evento

## 1. Objetivo y Contexto

### Objetivo
Implementar el sistema de generación de certificados de participación y el módulo de encuestas de satisfacción post-evento, permitiendo a los participantes obtener constancia de su asistencia y a los organizadores recopilar feedback para mejorar futuros eventos.

### Contexto
Una vez finalizado un evento académico, es necesario:
1. **Certificados**: Los participantes necesitan constancia formal de su participación, ya sea como asistentes, aprobantes, autores o expositores. Estos certificados deben ser verificables para terceros.
2. **Encuestas**: Los organizadores necesitan recopilar feedback sobre la calidad del evento, la organización, los disertantes y otros aspectos relevantes para mejorar ediciones futuras.
3. **Comentarios**: Los participantes pueden dejar comentarios públicos o privados sobre el evento.
4. **Informes**: Los organizadores necesitan informes consolidados del evento con estadísticas y agendas.

### Alcance
- Generación de certificados con tipos: asistencia, aprobación, autor, expositor
- Descarga de certificados en formato PDF
- Verificación pública de certificados
- Creación de encuestas personalizadas por evento
- Respuesta a encuestas por participantes
- Sistema de comentarios con rating
- Generación de informes del evento (general, asistencia, satisfacción, agenda)

---

## 2. Historias de Usuario y Criterios de Aceptación

### HU-01: Generar Certificado
**Como** participante acreditado de un evento finalizado  
**Quiero** generar un certificado de mi participación  
**Para** tener constancia formal del evento  

**Criterios de Aceptación:**
- Dado que el evento finalizó y asistí, entonces puedo generar un certificado de asistencia
- Dado que aprobé el evento (si aplica), entonces puedo generar un certificado de aprobación
- Dado que fui autor de un trabajo presentado, entonces puedo generar un certificado de autor
- Dado que fui expositor, entonces puedo generar un certificado de expositor
- Dado que genero un certificado, entonces se crea con un código de verificación único
- Dado que ya generé un certificado del mismo tipo, entonces veo el existente en lugar de crear uno nuevo
- Dado que el evento no finalizó, entonces no puedo generar certificados

### HU-02: Descargar Certificado PDF
**Como** titular de un certificado  
**Quiero** descargar mi certificado en formato PDF  
**Para** imprimirlo o compartirlo  

**Criterios de Aceptación:**
- Dado que tengo un certificado generado, entonces puedo descargarlo como PDF
- Dado que descargo el PDF, entonces contiene mi nombre, nombre del evento, fecha, tipo de certificado y código de verificación
- Dado que abro el PDF, entonces tiene un diseño profesional con el logo del evento (si existe)
- Dado que el PDF es generado, entonces tiene un nombre de archivo descriptivo

### HU-03: Verificar Certificado
**Como** tercero (empleador, institución)  
**Quiero** verificar la autenticidad de un certificado  
**Para** confirmar que es legítimo  

**Criterios de Aceptación:**
- Dado que ingreso un código de verificación válido, entonces veo los datos del certificado
- Dado que el código es válido, entonces veo nombre del titular, evento, fecha y tipo
- Dado que el código no existe, entonces veo un mensaje de certificado no encontrado
- Dado que verifico un certificado, entonces la verificación es pública (no requiere login)

### HU-04: Crear Encuesta
**Como** organizador de un evento  
**Quiero** crear una encuesta de satisfacción  
**Para** recopilar feedback de los participantes  

**Criterios de Aceptación:**
- Dado que accedo a crear una encuesta, entonces puedo definir título y descripción
- Dado que creo preguntas, entonces puedo elegir entre tipos: rating, multiple choice, texto, likert
- Dado que creo preguntas de tipo multiple choice o likert, entonces puedo definir las opciones
- Dado que marco una pregunta como requerida, entonces los participantes deben responderla
- Dado que guardo la encuesta, entonces queda activa para el evento
- Dado que ya existe una encuesta activa, entonces puedo editarla o crear una nueva versión

### HU-05: Responder Encuesta
**Como** participante de un evento finalizado  
**Quiero** responder la encuesta de satisfacción  
**Para** dar mi opinión sobre el evento  

**Criterios de Aceptación:**
- Dado que el evento finalizó y hay una encuesta activa, entonces puedo acceder a ella
- Dado que hay preguntas requeridas sin responder, entonces no puedo enviar la encuesta
- Dado que envío la encuesta completada, entonces recibo confirmación de envío
- Dado que ya respondí la encuesta, entonces no puedo responderla nuevamente
- Dado que envío la encuesta, entonces queda asociada a mi inscripción

### HU-06: Dejar Comentario
**Como** participante de un evento  
**Quiero** dejar un comentario y rating sobre el evento  
**Para** compartir mi experiencia  

**Criterios de Aceptación:**
- Dado que el evento finalizó y participé, entonces puedo dejar un comentario
- Dado que dejo un comentario, entonces puedo incluir un rating de 1 a 5 estrellas
- Dado que envío un comentario, entonces aparece en la sección de comentarios del evento
- Dado que dejo un comentario, entonces mi nombre aparece de forma anónima (solo iniciales)
- Dado que el rating promedio del evento se calcula automáticamente, entonces se muestra en el detalle

### HU-07: Generar Informe del Evento
**Como** organizador de un evento  
**Quiero** generar informes del evento  
**Para** tener datos consolidados de participación y satisfacción  

**Criterios de Aceptación:**
- Dado que accedo a generar informes, entonces puedo elegir el tipo: general, asistencia, satisfacción, agenda
- Dado que genero un informe general, entonces veo resumen de inscripciones, acreditaciones y asistencia
- Dado que genero un informe de satisfacción, entonces veo resultados de encuestas y comentarios
- Dado que genero un informe de agenda, entonces veo la programación del evento con disertantes
- Dado que el evento tiene datos suficientes, entonces los gráficos se generan correctamente
- Dado que genero un informe, entonces puedo exportarlo en formato JSON

### HU-08: Ver Resultados de Encuestas
**Como** organizador de un evento  
**Quiero** ver los resultados de las encuestas  
**Para** analizar el feedback recibido  

**Criterios de Aceptación:**
- Dado que accedo a los resultados, entonces veo estadísticas por pregunta
- Dado que hay preguntas de tipo rating, entonces veo el promedio y distribución
- Dado que hay preguntas de texto, entonces veo las respuestas de forma anónima
- Dado que hay pocas respuestas, entonces veo un aviso sobre la muestra

---

## 3. Requisitos Funcionales y Reglas de Negocio

### Requisitos Funcionales
- **RF-01**: El sistema debe generar certificados con código de verificación único
- **RF-02**: El sistema debe soportar 4 tipos de certificado: asistencia, aprobación, autor, expositor
- **RF-03**: El sistema debe generar PDFs de certificados con diseño profesional
- **RF-04**: El sistema debe permitir verificación pública de certificados por código
- **RF-05**: El sistema debe permitir crear encuestas con diferentes tipos de preguntas
- **RF-06**: El sistema debe permitir a los participantes responder encuestas una sola vez
- **RF-07**: El sistema debe permitir comentarios con rating en escala 1-5
- **RF-08**: El sistema debe calcular y mostrar el rating promedio del evento
- **RF-09**: El sistema debe generar informes en 4 tipos: general, asistencia, satisfacción, agenda
- **RF-10**: El sistema debe mostrar resultados de encuestas al organizador

### Reglas de Negocio
- **RN-01**: Solo se pueden generar certificados para eventos finalizados
- **RN-02**: Solo participantes con inscripción confirmada y attended=true pueden generar certificado de asistencia
- **RN-03**: Un participante solo puede generar un certificado de cada tipo por evento
- **RN-04**: El código de verificación de certificado debe ser único y de 16 caracteres
- **RN-05**: Las encuestas solo se pueden responder después de que el evento finaliza
- **RN-06**: Un participante solo puede responder una encuesta por evento
- **RN-07**: Las preguntas marcadas como requeridas deben ser respondidas para enviar la encuesta
- **RN-08**: Los comentarios se muestran con identidad parcial (solo iniciales del nombre)
- **RN-09**: Solo el organizador puede crear encuestas para su evento
- **RN-10**: El rating promedio se calcula solo sobre comentarios con rating válido (1-5)
- **RN-11**: Los informes solo están disponibles para eventos finalizados
- **RN-12**: Los certificados no se pueden eliminar una vez generados

---

## 4. Restricciones Técnicas Específicas

### Backend
- Implementar endpoints RESTful según Contracts.md sección 5
- Usar librería `pdfkit` o `@react-pdf/renderer` para generación de PDFs
- Generar códigos de verificación con `crypto.randomBytes` (16 caracteres hexadecimales)
- Implementar stream de PDF para endpoint de descarga
- Calcular estadísticas de encuestas en servicio dedicado
- Implementar agregaciones para rating promedio y distribución

### Frontend
- Crear componente `CertificateList` para listar certificados del usuario
- Crear componente `SurveyForm` para responder encuestas
- Crear componente `SurveyBuilder` para crear encuestas (organizador)
- Crear componente `CommentsSection` con rating y listado
- Crear componente `EventReport` con gráficos y estadísticas
- Usar librería `recharts` para gráficos de resultados
- Implementar vista previa del certificado antes de descargar
- Rating con estrellas interactivas (componente `StarRating`)

### Base de Datos
- Tabla `certificates` con índice único en `verificationCode`
- Tabla `surveys` con relación a `events`
- Tabla `survey_questions` con relación a `surveys`
- Tabla `survey_responses` con relación a `surveys` y `registrations`
- Tabla `comments` con índice en `eventId` y `rating`
- Agregar campo `averageRating` calculado en Event (o calcular on-the-fly)

### Performance
- Generación de PDF: < 2 segundos
- Cálculo de estadísticas de encuesta: usar agregaciones de base de datos
- Paginar respuestas de comentarios (20 por página)
- Cache de rating promedio del evento (invalidar al crear comentario)

---

## 5. Modelo de Datos

```prisma
model Certificate {
  id               String            @id @default(uuid())
  registrationId   String
  userId           String
  eventId          String
  type             CertificateType
  issuedAt         DateTime          @default(now())
  verificationCode String            @unique
  pdfUrl           String?

  registration     Registration      @relation(fields: [registrationId], references: [id])
  user             User              @relation(fields: [userId], references: [id])
  event            Event             @relation(fields: [eventId], references: [id])

  @@index([userId])
  @@index([eventId])
  @@index([verificationCode])
  @@unique([registrationId, type])
  @@map("certificates")
}

model Survey {
  id          String           @id @default(uuid())
  eventId     String
  title       String
  description String
  isActive    Boolean          @default(true)
  createdAt   DateTime         @default(now())
  updatedAt   DateTime         @updatedAt

  event       Event            @relation(fields: [eventId], references: [id], onDelete: Cascade)
  questions   SurveyQuestion[]
  responses   SurveyResponse[]

  @@index([eventId])
  @@map("surveys")
}

model SurveyQuestion {
  id        String     @id @default(uuid())
  surveyId  String
  question  String
  type      QuestionType
  options   String[]
  required  Boolean    @default(false)
  order     Int

  survey    Survey     @relation(fields: [surveyId], references: [id], onDelete: Cascade)
  answers   SurveyAnswer[]

  @@index([surveyId])
  @@map("survey_questions")
}

model SurveyResponse {
  id             String         @id @default(uuid())
  surveyId       String
  registrationId String
  userId         String
  submittedAt    DateTime       @default(now())

  survey         Survey         @relation(fields: [surveyId], references: [id], onDelete: Cascade)
  registration   Registration   @relation(fields: [registrationId], references: [id])
  user           User           @relation(fields: [userId], references: [id])
  answers        SurveyAnswer[]

  @@index([surveyId])
  @@index([registrationId])
  @@unique([registrationId, surveyId])
  @@map("survey_responses")
}

model SurveyAnswer {
  id         String   @id @default(uuid())
  responseId String
  questionId String
  value      String
  comment    String?

  response   SurveyResponse @relation(fields: [responseId], references: [id], onDelete: Cascade)
  question   SurveyQuestion @relation(fields: [questionId], references: [id])

  @@index([responseId])
  @@index([questionId])
  @@map("survey_answers")
}

model Comment {
  id        String   @id @default(uuid())
  eventId   String
  userId    String
  content   String
  rating    Int?
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  event     Event    @relation(fields: [eventId], references: [id], onDelete: Cascade)
  user      User     @relation(fields: [userId], references: [id])

  @@index([eventId])
  @@index([rating])
  @@map("comments")
}

enum CertificateType {
  asistencia
  aprobacion
  autor
  expositor
}

enum QuestionType {
  rating
  multiple_choice
  text
  likert
}
```

### Modelo de Respuesta API (TypeScript)

```typescript
interface CertificateResponse {
  id: string;
  registrationId: string;
  userId: string;
  eventId: string;
  type: CertificateType;
  issuedAt: string;
  verificationCode: string;
  pdfUrl?: string;
  event?: {
    id: string;
    title: string;
    startDate: string;
    endDate: string;
  };
}

interface CertificateVerificationResponse {
  valid: boolean;
  certificate?: {
    id: string;
    type: CertificateType;
    issuedAt: string;
    verificationCode: string;
  };
  event?: {
    id: string;
    title: string;
    startDate: string;
    endDate: string;
  };
  user?: {
    id: string;
    firstName: string;
    lastName: string;
  };
}

interface SurveyResponse {
  id: string;
  surveyId: string;
  registrationId: string;
  userId: string;
  answers: SurveyAnswer[];
  submittedAt: string;
}

interface SurveyWithQuestions {
  survey: {
    id: string;
    eventId: string;
    title: string;
    description: string;
    isActive: boolean;
  };
  questions: {
    id: string;
    surveyId: string;
    question: string;
    type: QuestionType;
    options?: string[];
    required: boolean;
    order: number;
  }[];
}

interface CommentResponse {
  id: string;
  eventId: string;
  userId: string;
  content: string;
  rating?: number;
  createdAt: string;
  updatedAt: string;
  user: {
    firstName: string;
    lastName: string;
    avatar?: string;
  };
}

interface EventReportResponse {
  reportType: string;
  generatedAt: string;
  content: {
    summary: {
      totalRegistrations: number;
      totalAttendees: number;
      attendanceRate: number;
      averageRating?: number;
      totalComments: number;
    };
    statistics: Record<string, unknown>;
    agenda?: {
      time: string;
      title: string;
      speaker?: string;
    }[];
  };
}
```

---

## 6. Plan de Tareas

### Tarea 1: Modelo de Datos y Migraciones
- [ ] Definir modelo `Certificate` en Prisma schema
- [ ] Definir modelos `Survey`, `SurveyQuestion`, `SurveyResponse`, `SurveyAnswer`
- [ ] Definir modelo `Comment`
- [ ] Definir enums `CertificateType`, `QuestionType`
- [ ] Crear migración
- [ ] Crear seeds con certificados, encuestas y comentarios de ejemplo
- [ ] **Branch**: `feature/spec-04-data-model`

### Tarea 2: Backend - Certificados
- [ ] Implementar Zod schemas para Certificate
- [ ] Implementar servicio de generación de códigos de verificación
- [ ] Implementar `POST /certificates` (generar certificado)
- [ ] Implementar `GET /certificates` (listar certificados del usuario)
- [ ] Implementar `GET /certificates/:id/download` (descargar PDF)
- [ ] Implementar `GET /certificates/verify/:code` (verificar - público)
- [ ] Servicio de generación de PDF con template
- [ ] Validar elegibilidad para certificado (evento finalizado, attended)
- [ ] **Branch**: `feature/spec-04-backend-certificates`

### Tarea 3: Backend - Encuestas
- [ ] Implementar Zod schemas para Survey y preguntas
- [ ] Implementar `POST /events/:eventId/surveys` (crear encuesta)
- [ ] Implementar `GET /events/:eventId/survey` (obtener encuesta activa)
- [ ] Implementar `POST /events/:eventId/survey/responses` (responder)
- [ ] Validar que usuario no haya respondido antes
- [ ] Validar preguntas requeridas
- [ ] Implementar servicio de cálculo de estadísticas
- [ ] **Branch**: `feature/spec-04-backend-surveys`

### Tarea 4: Backend - Comentarios e Informes
- [ ] Implementar `POST /events/:eventId/comments` (crear comentario)
- [ ] Implementar `GET /events/:eventId/comments` (listar comentarios)
- [ ] Calcular y retornar rating promedio en respuesta
- [ ] Implementar `GET /events/:eventId/report` (generar informe)
- [ ] Servicio de generación de informes por tipo
- [ ] **Branch**: `feature/spec-04-backend-comments-reports`

### Tarea 5: Frontend - Certificados
- [ ] Crear componente `CertificateList` con tarjetas
- [ ] Implementar botón de descarga de PDF
- [ ] Implementar botón de generar certificado (si elegible)
- [ ] Crear componente `CertificateVerification` para verificación pública
- [ ] Página pública de verificación de certificados
- [ ] **Branch**: `feature/spec-04-frontend-certificates`

### Tarea 6: Frontend - Encuestas
- [ ] Crear componente `SurveyForm` para responder encuestas
- [ ] Implementar diferentes tipos de preguntas (rating, multiple choice, text, likert)
- [ ] Validación de preguntas requeridas
- [ ] Crear componente `SurveyBuilder` para organizadores
- [ ] Agregar preguntas dinámicamente
- [ ] Crear componente `SurveyResults` para ver resultados (organizador)
- [ ] **Branch**: `feature/spec-04-frontend-surveys`

### Tarea 7: Frontend - Comentarios e Informes
- [ ] Crear componente `CommentsSection` con formulario y listado
- [ ] Implementar componente `StarRating` interactivo
- [ ] Mostrar rating promedio del evento
- [ ] Crear componente `EventReport` con gráficos (recharts)
- [ ] Gráficos de asistencia, satisfacción, distribución de ratings
- [ ] Integrar informes en panel de organizador
- [ ] **Branch**: `feature/spec-04-frontend-comments-reports`

### Tarea 8: Pruebas
- [ ] Tests unitarios de generación de certificados
- [ ] Tests unitarios de cálculo de estadísticas de encuestas
- [ ] Tests de integración de endpoints de certificados
- [ ] Tests de integración de endpoints de encuestas
- [ ] Tests de integración de comentarios
- [ ] Tests E2E de flujo completo de certificados
- [ ] Tests E2E de flujo completo de encuestas
- [ ] Tests E2E de comentarios y rating
- [ ] **Branch**: `feature/spec-04-tests`

---

## 7. Estrategia de Verificación

### Pruebas Unitarias
- **Generación de códigos**: Verificar formato único de 16 caracteres
- **Elegibilidad de certificado**: Verificar reglas (evento finalizado, attended, no duplicado)
- **Cálculo de rating promedio**: Verificar con diferentes conjuntos de datos
- **Validación de encuestas**: Verificar preguntas requeridas, una respuesta por usuario
- **Generación de PDF**: Verificar que el stream de PDF es válido

### Pruebas de Integración
- **Certificados**: Verificar creación, listado, descarga y verificación
- **Encuestas**: Verificar creación, respuesta, y restricción de respuesta única
- **Comentarios**: Verificar creación, listado con rating, y cálculo de promedio
- **Informes**: Verificar cada tipo de informe con datos de ejemplo

### Pruebas E2E (Playwright)
- **Flujo de certificado**: Login -> Evento finalizado -> Generar certificado -> Descargar PDF -> Verificar código
- **Flujo de encuesta**: Login -> Evento finalizado -> Ver encuesta -> Responder -> Ver confirmación
- **Flujo de comentarios**: Login -> Evento finalizado -> Dejar comentario con rating -> Ver en listado
- **Flujo de informe**: Login como organizador -> Generar informe -> Ver estadísticas
- **Verificación pública**: Acceder sin login -> Ingresar código -> Ver datos del certificado

### Criterios de Aceptación de la Spec
- [ ] Todos los endpoints de la sección 5 de Contracts.md implementados
- [ ] Cobertura de tests > 80%
- [ ] Todos los tests pasando
- [ ] Generación y descarga de certificados PDF funcionando
- [ ] Verificación pública de certificados funcionando
- [ ] Creación y respuesta de encuestas funcionando
- [ ] Sistema de comentarios con rating funcionando
- [ ] Generación de informes funcionando
- [ ] Resultados de encuestas visibles para organizadores
