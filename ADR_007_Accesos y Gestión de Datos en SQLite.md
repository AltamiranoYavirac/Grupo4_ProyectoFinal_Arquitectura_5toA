# ADR 007: Acceso y Gestión de Datos en SQLite

*Autores:* Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

*Asignatura:* Arquitectura de Software | 5 DS “A”

### Fecha:
2025-10-20

### Estado:
Aceptado

## Contexto:

La base de datos local almacena la información médica (medicinas, citas, horarios, recordatorios, configuraciones). Es fundamental contar con una capa de acceso a datos bien estructurada para evitar duplicación de lógica y garantizar consistencia e integridad de la información.

## Decisión:

Implementar un Data Access Layer (DAL) mediante el patrón Repository, utilizando el paquete sqflite para la ejecución de operaciones SQL.
Cada entidad (por ejemplo, Medicina, Cita, Recordatorio) contará con su propio DAO (Data Access Object) para manejar operaciones CRUD.

## Consecuencias:

Centraliza toda la lógica de acceso a datos.

Facilita la reutilización de código y pruebas unitarias.

Permite evolucionar el esquema de base de datos con migraciones controladas.

Reduce el acoplamiento entre la interfaz y la base de datos.

Aumenta la claridad en el flujo de datos entre capas.

# Alternativas Consideradas:
## Alternativa 1: Drift (ORM sobre SQLite)

### Pros:

Queries seguras y generadas automáticamente.

Migraciones más simples.

## Contras:

Curva de aprendizaje más alta.

Aumenta el tiempo de compilación por generación de código.

## Alternativa 2: Hive (NoSQL)

### Pros:

API muy simple.

Rápido para lecturas y escrituras pequeñas.

## Contras:

Falta de soporte para queries relacionales complejas.

Difícil manejo de relaciones entre entidades.

## Descripción:

La capa de datos se organiza así:

lib/
 ├── data/
 │   ├── database_helper.dart        # Configuración inicial de SQLite
 │   ├── dao/
 │   │   ├── medicina_dao.dart
 │   │   ├── cita_dao.dart
 │   │   └── recordatorio_dao.dart
 │   ├── models/
 │   │   ├── medicina.dart
 │   │   ├── cita.dart
 │   │   └── recordatorio.dart
 │   └── repositories/
 │       ├── medicina_repository.dart
 │       ├── cita_repository.dart
 │       └── recordatorio_repository.dart


Cada DAO maneja operaciones CRUD sobre su tabla correspondiente, mientras que los repositorios coordinan la lógica de negocio y validaciones antes de acceder a los datos.

## Razón de la Decisión:

El patrón Repository ofrece un equilibrio entre simplicidad y organización, aislando las consultas SQL del resto de la aplicación. Esta estructura permite mantener la base de datos sincronizada con las alarmas y notificaciones sin duplicar lógica. Además, facilita la extensión futura del sistema, como estadísticas de adherencia o exportación de datos, sin afectar las capas superiores.