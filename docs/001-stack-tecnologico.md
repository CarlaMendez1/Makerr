# ADR 001: Selección del Stack Tecnológico

**Fecha:** 2026-07-05
**Estado:** Aceptado

## Contexto
Para el desarrollo del sistema *Academic Event Manager* definido en **Avellaneda-Mendez-TP2-IS3ACI (1).pdf**, el equipo necesita definir una base técnica sólida que permita manejar la lógica de gestión de eventos, inscripciones, y el listado público con filtros. Se requiere una solución que sea escalable, mantenible y eficiente para las necesidades del Trabajo Práctico 2.

## Decisión
Hemos decidido implementar el siguiente stack tecnológico:
*   **Backend:** Node.js con el framework NestJS. Se elige por su arquitectura modular, que facilita la separación de responsabilidades (controladores, servicios, módulos), ideal para cumplir con los Requisitos Funcionales (RF) especificados en **Avellaneda-Mendez-TP2-IS3ACI (1).pdf**.
*   **Lenguaje:** TypeScript, para asegurar la calidad del código mediante tipado estático, reduciendo errores en tiempo de ejecución.
*   **Base de Datos:** PostgreSQL. Es un sistema relacional robusto que permite manejar correctamente las relaciones entre eventos, usuarios e inscripciones, manteniendo la integridad de los datos.
*   **Frontend:** React, para crear una interfaz de usuario dinámica y responsiva, cumpliendo con la necesidad de paginación y filtros descritos en las Historias de Usuario (HU).

## Consecuencias
### Positivas
*   La modularidad de NestJS permite a los integrantes del equipo trabajar en diferentes módulos (Eventos, Inscripciones) de forma aislada.
*   TypeScript proporciona una mayor seguridad en el manejo de los estados del evento (ej: `borrador`, `publicado`, `en_curso`, `finalizado`).
*   La estructura relacional de PostgreSQL se alinea perfectamente con las reglas de negocio (RN) de inscripción y gestión de usuarios.

### Negativas / Riesgos
*   Requiere una configuración inicial más compleja que otros frameworks ligeros.
*   Se necesita una curva de aprendizaje para dominar la inyección de dependencias propia de NestJS.
