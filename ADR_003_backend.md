# ADR 003: Tecnología de Backend - Base de Datos Local

### Fecha:
2025-10-14

### Estado:
Aceptado

## Contexto:
La aplicación requiere almacenar de manera persistente información estructurada que incluye datos de medicinas (nombre, dosis, frecuencia, instrucciones), citas médicas (fecha, hora, doctor, especialidad, lugar, notas), horarios de alarmas programadas, y posiblemente un historial de recordatorios completados o pospuestos. Esta información debe ser accesible inmediatamente sin latencia de red, debe persistir entre reinicios de la aplicación, y necesita soportar consultas complejas para filtrar y ordenar datos (por ejemplo, "medicinas de hoy", "próximas citas", "historial del último mes"). Al tratarse de información médica sensible, la privacidad y seguridad son prioritarias.

## Decisión:
Implementar SQLite como sistema de gestión de base de datos local utilizando el paquete sqflite de Flutter para toda la persistencia de datos de la aplicación.

## Consecuencias:
- Toda la información estará disponible offline sin depender de conectividad a internet
- Los datos médicos sensibles permanecerán exclusivamente en el dispositivo del usuario
- Se podrán realizar consultas SQL complejas con JOINs, filtros y ordenamiento eficiente
- Será posible establecer relaciones claras entre entidades (medicinas, horarios, alertas)
- Se requerirá escribir código SQL para definir esquemas y realizar operaciones
- Será necesario implementar un sistema de migraciones para evolucionar el esquema de base de datos
- No habrá sincronización automática entre dispositivos si el usuario cambia de teléfono
- Se deberá implementar manualmente la lógica de backup y restauración si se desea

# Alternativas a Considerar

## Alternativa 1: Hive

### Pros:
- Base de datos NoSQL extremadamente rápida escrita en Dart puro
- Sin dependencias nativas, funciona idénticamente en todas las plataformas
- API muy simple e intuitiva, más fácil de aprender que SQL
- Excelente rendimiento para operaciones de lectura y escritura
- Soporte para tipos de datos personalizados mediante adaptadores
- Almacenamiento basado en clave-valor con colecciones (boxes)
- Código más limpio y conciso para operaciones básicas
- No requiere migraciones complejas de esquema

### Contras:
- Consultas complejas son más difíciles sin lenguaje SQL estructurado
- Filtrado y ordenamiento requiere cargar datos en memoria
- Menos potente para relaciones complejas entre múltiples entidades
- Comunidad más pequeña comparada con SQLite
- Menos documentación y ejemplos disponibles
- No tan apropiado para consultas con múltiples condiciones y JOINs

## Alternativa 2: Drift (anteriormente Moor)

### Pros:
- Capa de abstracción sobre SQLite con generación de código Dart
- Sintaxis type-safe que previene errores en tiempo de compilación
- Escribir consultas en Dart en lugar de SQL puro
- Migraciones de esquema más sencillas y automáticas
- Soporte para streams reactivos de datos
- Mejor integración con el ecosistema Flutter/Dart
- Validación automática de esquema
- Queries más legibles y mantenibles

### Contras:
- Capa adicional de abstracción añade complejidad
- Curva de aprendizaje adicional sobre SQLite básico
- Generación de código puede aumentar tiempo de compilación
- Puede ser excesivo para esquemas de base de datos simples
- Debugging más complejo al tener código generado
- Ligeramente más pesado que SQLite puro

## Alternativa 3: ObjectBox

### Pros:
- Base de datos NoSQL de alto rendimiento optimizada para móviles
- Extremadamente rápida, especialmente para grandes volúmenes de datos
- API orientada a objetos muy intuitiva
- Sincronización en tiempo real disponible (ObjectBox Sync)
- No requiere escribir SQL
- Consultas mediante API fluida de Dart
- Soporte nativo para relaciones entre objetos
- Excelente rendimiento en operaciones complejas

### Contras:
- Requiere agregar dependencias nativas específicas de plataforma
- Comunidad significativamente más pequeña que SQLite
- Documentación menos extensa
- ObjectBox Sync es un servicio de pago
- Menos familiar para desarrolladores con experiencia SQL
- Mayor tamaño de la aplicación debido a bibliotecas nativas
- Menos ejemplos y recursos de aprendizaje disponibles

## Descripción:
SQLite es un sistema de gestión de bases de datos relacional embebido, sin servidor y autocontenido que almacena toda la base de datos en un único archivo. Es el motor de base de datos más desplegado en el mundo, incluido por defecto en Android e iOS. En Flutter, se accede mediante el paquete sqflite que proporciona una API asíncrona para ejecutar comandos SQL. SQLite soporta el estándar SQL con capacidades para crear tablas con esquemas definidos, establecer restricciones de integridad, crear índices para optimización, definir claves foráneas para relaciones, y ejecutar transacciones ACID que garantizan consistencia de datos. El paquete sqflite maneja automáticamente la creación, apertura y cierre de la conexión a la base de datos, y proporciona métodos convenientes para operaciones comunes.

## Razón de la Decisión:
SQLite fue seleccionado como solución de base de datos porque ofrece la combinación ideal de madurez, funcionalidad y familiaridad para las necesidades del proyecto. Es la solución más probada y estable, utilizada por millones de aplicaciones en producción durante décadas, lo que garantiza confiabilidad absoluta para almacenar información médica crítica. La capacidad de escribir consultas SQL estándar permite realizar operaciones complejas eficientemente, como buscar todas las medicinas programadas para un horario específico con información relacionada del recordatorio, o generar reportes de adherencia filtrando por rangos de fechas. Las relaciones entre medicinas, horarios, alertas y citas se modelan naturalmente mediante claves foráneas y JOINs, manteniendo integridad referencial. SQLite es extremadamente ligero con overhead mínimo, consume poca batería, y no requiere procesos en segundo plano. Al ser incluido nativamente en Android e iOS, no añade tamaño significativo a la aplicación. La amplia adopción de SQLite significa que hay abundante documentación, tutoriales, y soluciones a problemas comunes disponibles. Para desarrolladores con experiencia en bases de datos relacionales, la curva de aprendizaje es mínima. Aunque alternativas como Hive o ObjectBox ofrecen mejor rendimiento bruto, la diferencia es imperceptible para el volumen de datos esperado (cientos de registros, no millones), mientras que SQLite proporciona mayor flexibilidad en consultas complejas que serán necesarias para funcionalidades futuras como estadísticas de adherencia o reportes médicos.