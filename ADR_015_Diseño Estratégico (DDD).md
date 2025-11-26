# ADR 015: Diseño Estratégico (DDD)

**Autores:** Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

**Asignatura:** Arquitectura de Software | 5 DS “A”

**Fecha:** 22–11–2025

**Estado:** Aceptado

# Contextos Delimitados (Bounded Contexts)

En el proyecto, el dominio se divide en los siguientes subdominios lógicos, cada uno con su propio modelo, reglas y responsabilidades:

### 1. Medicinas (Medicine Management) – Subdominio Principal (Core Domain)

Responsable de gestionar toda la información de medicamentos: creación, dosis, frecuencia y estado.

### 2. Citas Médicas (Appointments) – Subdominio Principal

Gestiona las citas del usuario: fecha, hora, médico, motivo y recordatorios asociados.

### 3. Alarmas / Recordatorios (Reminders) – Subdominio de Soporte (Supporting Domain)

Núcleo de la programación y ejecución de alarmas para medicinas y citas.

### 4. Historial de Adherencia (Adherence History) – Subdominio de Soporte

Registra las acciones del usuario (medicina tomada, omitida, pospuesta, alarma completada).

### 5. Configuración del Usuario (User Settings) – Shared Kernel

Subdominio compartido por toda la aplicación. Incluye idioma, modo oscuro, sonido y accesibilidad.

### 6. Persistencia Local (SQLite) – Anti-Corruption Layer (ACL)

Abstrae el acceso a la base de datos local y evita acoplamiento entre los Bounded Contexts y el almacenamiento.

--- 
# Mapa de Contexto (Context Map)

```

 ┌──────────────────┐       OHS        ┌──────────────────────┐
 │  Medicinas       │ <--------------> │    Alarmas           │
 │  (Core Domain)   │                  │  (Supporting Domain) │
 └──────────────────┘                  └──────────────────────┘
          │                                   │
          │                                   │ Publica eventos
          │                                   ▼
          │                      ┌────────────────────────┐
          │                      │   Historial de         │
          └────── OHS ────────►  │  Adherencia            │
                                 │   (Supporting Domain)  │
                                 └────────────────────────┘

 ┌──────────────────┐
 │   Citas Médicas  │ ── OHS ──► Alarmas
 └──────────────────┘

 ┌──────────────────┐          Shared Kernel        ┌──────────────────┐
 │  Configuración   │ <-------------------------->  │   Todos los BCs  │
 └──────────────────┘                               └──────────────────┘

 ┌──────────────────┐        ACL       ┌─────────────────────┐
 │   Bounded        │ ---------------->│ Persistencia SQLite │
 │   Contexts       │                  │   (Anti-Corruption) │
 └──────────────────┘                  └─────────────────────┘
```
---

## Patrones de Integración usados en el Context Map
### 1. OHS – Open Host Service

**Usado por:**

Medicinas → Alarmas

Citas → Alarmas

Alarmas → Historial

Justificación:
Cada módulo expone operaciones estables como servicios internos formales (APIs internas), permitiendo interacción síncrona sin conocer detalles internos.

---
### 2. Shared Kernel

**Usado por:**

Todos los Bounded Contexts ↔ Configuración del Usuario.

Justificación:
Las preferencias del usuario afectan a toda la aplicación, por lo que se compartió un pequeño submodelo común, versionado conjuntamente.

---
### 3. ACL – Anti-Corruption Layer

**Usado entre:**

Todos los Bounded Contexts → Persistencia Local (SQLite)

Justificación:
Cada módulo evita depender de tablas SQL, consultas o la estructura interna de la base de datos.
La capa ACL provee operaciones genéricas:

- insertar

- actualizar

- eliminar

- consultar
---


## Resumen del Context Map (lista)

### Relaciones y patrones

| Relación                      | Patrón                  | Motivo                                                 |
| ----------------------------- | ----------------------- | ------------------------------------------------------ |
| Medicinas ↔ Alarmas           | Open Host Service (OHS) | Comunicación formal para programar recordatorios.      |
| Citas ↔ Alarmas               | OHS                     | Solicitud de alarmas para citas.                       |
| Alarmas → Historial           | OHS + Eventos internos  | Registrar adherencia cuando se completa una alarma.    |
| Configuración ↔ Todos         | Shared Kernel           | Preferencias compartidas que impactan todo el sistema. |
| Todos → Persistencia (SQLite) | Anti-Corruption Layer   | Desacoplar lógica del dominio de la base de datos.     |

