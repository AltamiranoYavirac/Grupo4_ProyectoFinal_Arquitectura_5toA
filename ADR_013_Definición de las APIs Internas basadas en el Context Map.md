# ADR 013: Definición de las APIs Internas basadas en el Context Map

**Autores:** Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

**Asignatura:** Arquitectura de Software | 5 DS “A”

**Fecha:** 22–11–2025

**Estado:** Aceptado

### Contexto

A partir del diseño estratégico descrito en el ADR 001, los Bounded Contexts del sistema deben comunicarse entre sí mediante contratos claros y estables.
Las operaciones entre módulos como Medicinas, Citas, Alarmas, Historial, Configuración y Persistencia no pueden depender de implementaciones concretas o acceso directo a datos internos.

Para evitar acoplamientos, se requieren APIs internas bien definidas, derivadas del Context Map, que funcionen como fronteras técnicas entre los Bounded Contexts.
Estas APIs permitirán que cada módulo evolucione de manera independiente, manteniendo coherencia del dominio y facilitando un futuro despliegue en la nube.

### Decisión

Se definieron APIs internas oficiales para cada Bounded Context.
Estas APIs funcionan como “contratos formales” y representan la forma en que los módulos intercambian información sin romper sus límites.

- Cada API se define mediante:

- Su objetivo dentro del dominio.

- Las operaciones principales que expone.

- Los datos esenciales que recibe o devuelve.

- La relación directa con otros Bounded Contexts según el Context Map.

- Las principales APIs acordadas fueron:

- API de Gestión de Medicinas

Permite crear, actualizar, eliminar y consultar medicinas.
Incluye la operación para solicitar a Alarmas la programación de recordatorios asociados a una medicina.

### API de Citas Médicas

Permite registrar y gestionar citas médicas.
Incluye la operación para solicitar a Alarmas la programación de una alarma para una cita.

### API de Alarmas (Recordatorios)

Es el núcleo de la programación y gestión de recordatorios.
Permite crear alarmas, cancelarlas, posponerlas y marcarlas como completadas.
Al completarse una alarma, publica un evento interno para que Historial lo registre.

### API de Historial de Adherencia

Permite registrar eventos como medicinas tomadas, omitidas o pospuestas.
También permite consultar el historial.
Consume eventos de la API de Alarmas.

### API de Configuración del Usuario

Expone las preferencias del usuario (modo oscuro, sonido, idioma, accesibilidad).
Al ser parte del Shared Kernel, puede ser utilizada por todos los Bounded Contexts.

### API de Persistencia Local (ACL)

Centraliza la comunicación con SQLite mediante operaciones CRUD abstractas.
Evita que los Bounded Contexts conozcan SQL o la estructura interna de la base de datos.

### Ejemplos de Operaciones por API

Sin formato técnico ni tablas, solo la descripción de operaciones esenciales:

**Gestión de Medicinas:** crear medicina, obtener medicina, actualizar medicina, eliminar medicina, solicitar programación de recordatorios.

**Citas Médicas:** crear cita, obtener cita, actualizar cita, eliminar cita, solicitar alarma para cita.

**Alarmas:** crear alarma, cancelar alarma, posponer alarma, marcar alarma como completada, listar alarmas activas.

**Historial:** registrar evento de adherencia, consultar historial completo o por ID.

**Configuración:** obtener configuración actual, actualizar preferencias del usuario.

**Persistencia Local:** insertar registro, eliminar registro, consultar datos, actualizar registros.

### Consecuencias

El sistema cuenta ahora con contratos técnicos bien definidos, lo cual:

- Permite que cada Bounded Context evolucione sin depender de la implementación interna de otros.

- Garantiza coherencia y claridad en la comunicación entre módulos.

- Facilita pruebas unitarias y pruebas de contrato.

- Prepara el sistema para sincronización en la nube, donde las APIs internas pueden migrar fácilmente a endpoints externos.

**Sin embargo, también implica:**

- Mayor esfuerzo de documentación y mantenimiento.

- La necesidad de respetar versión de APIs para evitar rupturas.

- Coordinación entre los equipos cuando se modifican las interfaces compartidas.

## Alternativas Consideradas
### Alternativa 1: Comunicación directa entre módulos

**Pros:** implementación rápida.
Contras: acoplamiento extremo, riesgos altos al modificar código, dificulta migración futura a backend o microservicios.

### Alternativa 2: Acceder directamente a la base de datos desde todos los módulos

**Pros:** simple de implementar.
Contras: rompe DDD, mezcla responsabilidades, genera inconsistencias y dificulta escalabilidad.

### Alternativa 3: Uso de un bus de eventos sin APIs

**Pros:** desacoplamiento total.
Contras: demasiada complejidad para una aplicación móvil offline; no resuelve operaciones síncronas.

**Clave:**
Se adopta una arquitectura basada en APIs internas alineadas con los Bounded Contexts definidos, respetando sus límites y relaciones del Context Map. Esto mantiene la modularidad del dominio y permite evolución a largo plazo.