# ADR 009: Estructura Interna de la Aplicación Flutter

*Autores:* Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

*Asignatura:* Arquitectura de Software | 5 DS “A”

### Fecha:
2025-10-20

### Estado:
Aceptado

## Contexto:
La aplicación Flutter constituye el contenedor principal de la interfaz de usuario y la lógica de interacción con el usuario final. Es responsable de la presentación visual, la navegación entre pantallas, la gestión de estado de los datos cargados desde la base de datos local, y la comunicación con el servicio de notificaciones.
Dado que la aplicación está orientada a personas mayores, se requiere una estructura interna clara, modular y fácil de mantener, con especial énfasis en la accesibilidad y la simplicidad de navegación.

## Decisión:
Adoptar una arquitectura basada en componentes modulares con patrón MVC (Modelo-Vista-Controlador), combinada con el uso de Provider como gestor de estado para la comunicación entre vistas y controladores.

## Consecuencias:
Separación clara de responsabilidades (UI, lógica y acceso a datos).

Facilita mantenimiento y escalabilidad del código.

Permite actualizaciones independientes de cada módulo (por ejemplo, vista de medicinas o configuración).

Mejora la legibilidad del código al aislar la lógica de negocio de la interfaz.

La curva de aprendizaje puede ser moderada para desarrolladores sin experiencia previa en Provider.

Requiere una planificación adecuada para manejar correctamente el ciclo de vida de los controladores y notificaciones de estado.

# Alternativas a Considerar

## Alternativa 1: BLoC (Business Logic Component)
### Pros:
*Alta escalabilidad y control detallado del flujo de datos.

*Excelente separación de responsabilidades.

*Amplio soporte en la comunidad Flutter.

## Contras:
Más complejo de implementar.

Curva de aprendizaje pronunciada.

Puede ser excesivo para el tamaño del proyecto.

## Alternativa 2: GetX
### Pros:
Código más simple y menos dependencias.

Actualizaciones de UI automáticas.

Menor cantidad de boilerplate.

## Contras:
Menos alineado con las prácticas oficiales de Flutter.

Riesgo de dependencias fuertes entre controladores y vistas.

## Descripción:
La aplicación se organiza en carpetas principales:
lib/
 ├── main.dart
 ├── models/         # Clases de datos (Medicamento, Cita, Recordatorio)
 ├── views/          # Pantallas y widgets (Home, Historial, Ajustes)
 ├── controllers/    # Lógica de negocio y controladores de vistas
 ├── providers/      # Gestión de estado con Provider
 ├── services/       # Acceso a notificaciones y base de datos
 └── utils/          # Constantes, temas, helpers y utilidades

## Razón de la Decisión:
El patrón MVC con Provider ofrece el equilibrio ideal entre simplicidad y modularidad para una aplicación de tamaño mediano. Mantiene el código organizado, facilita el testing y garantiza que la UI permanezca reactiva ante los cambios de estado. Dado el público objetivo (personas mayores), el enfoque modular permite mantener una experiencia fluida, accesible y fácil de mantener en el tiempo.
