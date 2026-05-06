# Project.md - Configuración General del Proyecto

## 1. Información del Proyecto

| Campo | Valor |
|-------|-------|
| Nombre | Academic Event Manager |
| Descripción | Aplicación web para la organización y gestión de eventos académicos |
| Versión | 1.0.0 |
| Tipo de Aplicación | Web (SPA + API REST) |

## 2. Stack Tecnológico

### Frontend
- **Framework**: React 18+ con TypeScript
- **Estilos**: Tailwind CSS
- **Routing**: React Router v6
- **Estado Global**: Zustand
- **HTTP Client**: Axios
- **Build Tool**: Vite

### Backend
- **Runtime**: Node.js 20 LTS
- **Framework**: Express.js
- **Lenguaje**: TypeScript
- **Base de Datos**: PostgreSQL 15+
- **ORM**: Prisma
- **Autenticación**: JWT (JSON Web Tokens)
- **Validación**: Zod

### Herramientas
- **Testing**: Vitest (unit), Playwright (E2E)
- **Linting**: ESLint + Prettier
- **Control de Versiones**: Git + GitHub
- **CI/CD**: GitHub Actions

## 3. Estructura del Repositorio

```
academic-event-manager/
├── README.md
├── Project.md
├── Contracts.md
├── specs/
│   ├── SPEC-01-gestion-eventos.md
│   ├── SPEC-02-inscripcion-participantes.md
│   ├── SPEC-03-gestion-roles-acreditacion.md
│   └── SPEC-04-certificados-encuestas.md
├── packages/
│   ├── client/                 # Frontend React
│   │   ├── src/
│   │   │   ├── components/
│   │   │   ├── pages/
│   │   │   ├── hooks/
│   │   │   ├── services/
│   │   │   ├── store/
│   │   │   ├── types/
│   │   │   └── utils/
│   │   └── package.json
│   └── server/                 # Backend Express
│       ├── src/
│       │   ├── controllers/
│       │   ├── routes/
│       │   ├── middleware/
│       │   ├── services/
│       │   ├── repositories/
│       │   ├── types/
│       │   └── utils/
│       └── package.json
└── prisma/
    └── schema.prisma
```

## 4. Convenciones de Código

### Naming
- **Archivos**: kebab-case (ej: `event-list.tsx`, `user-service.ts`)
- **Componentes React**: PascalCase (ej: `EventCard`, `RegistrationForm`)
- **Funciones/Variables**: camelCase
- **Constantes**: UPPER_SNAKE_CASE
- **Interfaces/Types**: PascalCase con prefijo `I` para interfaces (ej: `IUser`, `IEvent`)

### Commits
Seguir Conventional Commits:
```
<type>(<scope>): <description>

feat(event): add event creation endpoint
fix(auth): resolve token expiration issue
docs(spec): update SPEC-01 acceptance criteria
```

Tipos permitidos: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`

### Ramas
- `main` - Rama principal con código estable
- `develop` - Rama de integración
- `feature/<spec-id>-<descripcion>` - Ramas de feature (ej: `feature/spec-01-event-crud`)
- `bugfix/<descripcion>` - Correcciones de bugs

## 5. Convenciones de API

### Formato de URLs
- Usar sustantivos en plural para recursos
- Usar kebab-case para rutas compuestas
- Ejemplos:
  - `GET /api/events`
  - `GET /api/events/:id`
  - `POST /api/events/:id/registrations`

### Formato de Respuestas
```json
{
  "success": true,
  "data": {},
  "message": "Operación exitosa",
  "errors": []
}
```

### Códigos HTTP
- `200` - OK (GET, PUT, PATCH exitosos)
- `201` - Created (POST exitoso)
- `204` - No Content (DELETE exitoso)
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `409` - Conflict
- `500` - Internal Server Error

### Autenticación
- Todos los endpoints protegidos requieren header `Authorization: Bearer <token>`
- Los endpoints públicos se documentan explícitamente en Contracts.md

## 6. Base de Datos

### Convenciones
- Nombres de tablas en snake_case y plural (ej: `events`, `user_roles`)
- Primary key: `id` de tipo UUID
- Timestamps: `createdAt`, `updatedAt` en todas las tablas
- Foreign keys: `<nombreTabla>Id` (ej: `organizerId`, `eventId`)
- Soft deletes: campo `deletedAt` donde aplique

### Migraciones
- Cada cambio en el modelo debe incluir migración de Prisma
- Nombre descriptivo: `YYYYMMDDHHMMSS_descripcion`

## 7. Workflow de Desarrollo (Spec-Driven Development)

### Proceso
1. Leer la spec correspondiente
2. Verificar los contratos de API en Contracts.md
3. Implementar backend siguiendo los contratos
4. Implementar frontend consumiendo los endpoints
5. Escribir pruebas unitarias y de integración
6. Crear PR con referencia a la spec
7. Code review y merge a `develop`

### Pull Requests
- Titulo: `[SPEC-XX] Descripción corta`
- Descripción debe incluir:
  - Spec referenciada
  - Cambios realizados
  - Endpoints implementados
  - Pruebas ejecutadas

## 8. Variables de Entorno

### Backend (.env)
```
DATABASE_URL=postgresql://user:password@localhost:5432/academic_events
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=7d
PORT=3001
NODE_ENV=development
CORS_ORIGIN=http://localhost:5173
```

### Frontend (.env)
```
VITE_API_URL=http://localhost:3001/api
```

## 9. Reglas de Seguridad

- Nunca commitear archivos `.env` o credenciales
- Validar todas las entradas del usuario con Zod
- Implementar rate limiting en endpoints sensibles
- Usar HTTPS en producción
- Sanitizar inputs para prevenir XSS
- Implementar CORS correctamente
- Hashear contraseñas con bcrypt (costo 12)

## 10. Criterios de Definición de Terminado (DoD)

Una tarea se considera completada cuando:
- [ ] Código implementado siguiendo las specs
- [ ] Pruebas unitarias escritas y pasando (>80% coverage)
- [ ] Pruebas de integración para endpoints API
- [ ] Documentación de contratos actualizada si aplica
- [ ] Code review aprobado
- [ ] Merge a `develop` exitoso
- [ ] No hay warnings de linting o TypeScript
