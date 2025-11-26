# ADR 012: Definición del Context Map y APIs internas basadas en DDD

**Autores:** Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

**Asignatura:** Arquitectura de Software | 5 DS “A”

**Fecha:** 22–11–2025

**Estado:** Aceptado

## Contexto

La aplicación móvil de recordatorios médicos maneja diversos elementos del dominio: medicinas, citas médicas, alarmas, historial de adherencia, preferencias del usuario y persistencia local.

Se necesita evitar el acoplamiento excesivo y permitir un crecimiento ordenado del sistema, preparándolo para una futura sincronización en la nube y capacidades Serverless.

## Decisión

Se decidió aplicar Diseño Estratégico (DDD) para estructurar la aplicación:

Seis Bounded Contexts principales: Gestión de Medicinas, Citas Médicas, Alarmas, Persistencia Local (SQLite), Configuración e Historial.

Context Map: Define las relaciones estratégicas (OHS, Shared Kernel, ACL) entre los contextos.

APIs Internas: Funcionan como contratos formales derivados del Context Map para la comunicación entre módulos.

Esta arquitectura garantiza:

Separación de responsabilidades.

Consistencia semántica del dominio.

Reducción del acoplamiento técnico.

## Consecuencias

La aplicación queda correctamente modularizada y alineada con DDD.

Los Bounded Contexts pueden evolucionar independientemente o migrarse a microservicios en el futuro.

Se requiere un esfuerzo inicial mayor para documentar y mantener los contratos de las APIs.

Aumenta la complejidad técnica comparado con un diseño monolítico tradicional.

## Alternativas Consideradas

### Alternativa 1: Un solo Bounded Context (Monolítico)

**Pros:** 
- Implementación simple.
- Todo el código está centralizado.

**Contras:** 
- Acoplamiento extremo.
- Difícil evolución y escalabilidad por módulos.

### Alternativa 2: Dividir por pantallas

**Pros:** 
- Organización intuitiva para desarrolladores junior.

**Contras:** 
- No respeta las reglas del negocio.
- Mezcla responsabilidades de distintos dominios.

## Alternativa 3: Microservicios desde el inicio

**Pros:** 
- Escalabilidad total.

**Contras:** 
- Sobredimensionado para una app móvil offline.

Costos y complejidad de infraestructura innecesarios.

**Clave:** 
Se adopta una arquitectura de Bounded Contexts definidos por dominio, con relaciones expresadas en un Context Map y APIs internas derivadas, garantizando independencia evolutiva y preparación para la nube.