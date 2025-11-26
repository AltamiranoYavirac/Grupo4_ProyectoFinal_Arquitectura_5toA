# ADR 001: Proyecto de Aplicación de Recordatorios Médicos para Personas Mayores

*Autores:* Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

*Asignatura:* Arquitectura de Software | 5 DS “A”

### Fecha:
2025-10-14

### Estado:
Aceptado

## Contexto:
Se requiere desarrollar una aplicación móvil dirigida a personas mayores que les ayude a gestionar sus medicinas y citas médicas mediante un sistema de recordatorios y alarmas. El proyecto debe contemplar una solución tecnológica que sea eficiente de desarrollar, mantenible a largo plazo, accesible para el público objetivo, y que funcione de manera confiable sin dependencia de conexión a internet. La aplicación debe manejar datos sensibles de salud de forma segura y privada.

## Decisión:
Desarrollar una aplicación móvil multiplataforma utilizando Flutter como framework de desarrollo, Dart como lenguaje de programación, y SQLite como base de datos local para almacenamiento persistente de información.

## Consecuencias:
- Se desarrollará una única base de código que funcionará tanto en Android como en iOS
- La aplicación funcionará completamente offline, sin requerir conexión a internet
- Los datos médicos permanecerán almacenados localmente en el dispositivo del usuario, garantizando privacidad
- No habrá costos de infraestructura de servidor o servicios en la nube
- El rendimiento será nativo y las notificaciones se ejecutarán a nivel del sistema operativo
- Se requerirá que el equipo tenga conocimientos en Flutter/Dart y gestión de bases de datos locales
- La aplicación no sincronizará datos entre dispositivos de manera automática

# Alternativas a Considerar

## Alternativa 1: Desarrollo Nativo + Backend en la Nube

### Pros:
- Acceso completo a características nativas de cada plataforma
- Sincronización de datos entre dispositivos mediante servicios en la nube
- Backup automático de información médica
- Posibilidad de compartir datos con familiares o médicos

### Contras:
- Requiere desarrollar dos aplicaciones separadas (Android y iOS)
- Mayor tiempo y costo de desarrollo
- Necesita implementar y mantener infraestructura de backend
- Costos operativos continuos por servicios de servidor y base de datos en la nube
- Dependencia de conexión a internet
- Preocupaciones adicionales de seguridad y privacidad al almacenar datos médicos en servidores externos

## Alternativa 2: Aplicación Web Progresiva (PWA)

### Pros:
- Funciona en cualquier dispositivo con navegador web
- No requiere instalación desde tiendas de aplicaciones
- Actualizaciones instantáneas sin aprobación de stores
- Un único código para todas las plataformas incluyendo desktop

### Contras:
- Acceso limitado a características nativas del dispositivo (notificaciones, alarmas exactas)
- Rendimiento inferior comparado con aplicaciones nativas
- Experiencia de usuario menos fluida
- Notificaciones menos confiables, especialmente crítico para recordatorios médicos
- Soporte inconsistente entre diferentes navegadores y versiones

## Alternativa 3: React Native + Firebase

### Pros:
- Desarrollo multiplataforma con JavaScript/TypeScript
- Firebase proporciona backend como servicio completo
- Sincronización en tiempo real de datos
- Gran comunidad y ecosistema de librerías

### Contras:
- Rendimiento ligeramente inferior a Flutter
- Costos asociados a servicios Firebase según uso
- Dependencia de conexión a internet para funcionalidad completa
- Complejidad adicional al integrar múltiples servicios Firebase
- Almacenamiento de datos médicos sensibles en servidores de terceros

## Descripción:
El proyecto consiste en una aplicación móvil de recordatorios médicos construida con Flutter, un framework de código abierto desarrollado por Google que permite crear aplicaciones compiladas nativamente desde una única base de código. Flutter utiliza Dart como lenguaje de programación, ofreciendo desarrollo eficiente con características modernas y tipado fuerte. Para el almacenamiento de datos se utilizará SQLite, un sistema de base de datos relacional ligero y sin servidor que almacena toda la información localmente en el dispositivo. La arquitectura es completamente local, sin componentes backend en la nube, lo que garantiza funcionamiento offline y máxima privacidad de los datos médicos.

## Razón de la Decisión:
Se eligió esta arquitectura tecnológica porque ofrece el equilibrio óptimo entre eficiencia de desarrollo, funcionalidad y privacidad para el contexto específico de una aplicación de recordatorios médicos para personas mayores. Flutter permite desarrollar simultáneamente para Android e iOS con una única base de código, reduciendo significativamente tiempo y costos de desarrollo. La decisión de mantener todo local (sin backend en la nube) se fundamenta en tres pilares críticos: primero, garantizar funcionamiento offline confiable ya que las personas mayores pueden no tener acceso constante a internet; segundo, maximizar la privacidad manteniendo datos médicos sensibles exclusivamente en el dispositivo del usuario sin transmisiones a servidores externos; y tercero, eliminar costos operativos continuos y dependencias de servicios de terceros que podrían afectar la sostenibilidad del proyecto. SQLite proporciona capacidades robustas de base de datos relacional suficientes para gestionar medicinas, horarios y citas con consultas estructuradas, mientras que Flutter ofrece las herramientas necesarias para crear interfaces altamente accesibles adaptadas a las necesidades visuales y de usabilidad de personas mayores.