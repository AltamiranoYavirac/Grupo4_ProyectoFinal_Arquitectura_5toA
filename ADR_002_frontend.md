# ADR 002: Tecnologías de Frontend para la Aplicación Móvil

*Autores:* Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

*Asignatura:* Arquitectura de Software | 5 DS “A”

### Fecha:
2025-10-14

### Estado:
Aceptado

## Contexto:
Se necesita seleccionar un framework de desarrollo frontend que permita crear una interfaz de usuario accesible y fácil de usar para personas mayores. La interfaz debe incluir elementos como listas de medicinas, formularios para agregar citas, calendarios, y configuración de alarmas. Es fundamental que la aplicación funcione fluidamente en diferentes dispositivos Android e iOS, con rendimiento nativo que garantice transiciones suaves y respuesta inmediata a las interacciones del usuario. Además, se requiere que la solución permita implementar notificaciones locales confiables y acceso a características nativas del dispositivo.

## Decisión:
Utilizar Flutter como framework frontend con Dart como lenguaje de programación, complementado con el paquete flutter_local_notifications para gestión de notificaciones y alarmas.

## Consecuencias:
- Se dispondrá de un amplio conjunto de widgets personalizables para crear interfaces accesibles
- El rendimiento será cercano al nativo gracias a la compilación directa a código de máquina
- Una única base de código funcionará en Android e iOS manteniendo consistencia visual
- La función Hot Reload permitirá desarrollo rápido con visualización inmediata de cambios
- Se tendrá acceso completo a las APIs nativas de cada plataforma cuando sea necesario
- El tamaño de la aplicación será mayor (aproximadamente 20-30 MB) comparado con aplicaciones nativas puras
- Se requerirá aprendizaje de Flutter y Dart si el equipo no tiene experiencia previa

# Alternativas a Considerar

## Alternativa 1: React Native

### Pros:
- Desarrollo multiplataforma con JavaScript/TypeScript, lenguajes muy populares
- Enorme comunidad y ecosistema maduro con abundantes bibliotecas
- Muchos desarrolladores disponibles con experiencia en React
- Reutilización de componentes web existentes
- Hot Reloading para desarrollo rápido
- Soporte de Meta (Facebook) con actualizaciones continuas

### Contras:
- Rendimiento ligeramente inferior debido al puente JavaScript-Nativo
- Puede requerir escribir código nativo (Java/Kotlin/Swift) para funcionalidades específicas
- Debugging más complejo cuando hay problemas en el puente
- Mayor consumo de memoria comparado con Flutter
- Inconsistencias visuales entre plataformas si no se maneja cuidadosamente
- Animaciones complejas pueden presentar problemas de rendimiento

## Alternativa 2: Ionic con Capacitor

### Pros:
- Basado en tecnologías web estándar (HTML, CSS, JavaScript/TypeScript)
- Posibilidad de reutilizar código web existente
- Puede desplegarse como PWA además de app nativa
- Familiaridad para desarrolladores web
- Integración con frameworks populares como Angular, React o Vue
- Desarrollo y debugging más simple con herramientas de navegador

### Contras:
- Rendimiento significativamente inferior, especialmente en animaciones
- Experiencia de usuario menos nativa y fluida
- Notificaciones y alarmas menos confiables que con soluciones nativas
- Consumo de batería más elevado
- Limitaciones en acceso a características avanzadas del dispositivo
- No apropiado para aplicaciones que requieren alta fluidez visual

## Alternativa 3: Xamarin (ahora .NET MAUI)

### Pros:
- Desarrollo multiplataforma con C# y .NET
- Rendimiento nativo al compilar directamente
- Compartir código con aplicaciones de escritorio Windows
- Integración profunda con ecosistema Microsoft
- Fuerte tipado y herramientas de desarrollo robustas
- Acceso completo a APIs nativas

### Contras:
- Comunidad más pequeña comparada con Flutter o React Native
- Menos bibliotecas y componentes de terceros disponibles
- Microsoft está migrando de Xamarin a MAUI, causando incertidumbre
- Curva de aprendizaje pronunciada si no se tiene experiencia en .NET
- Documentación y recursos menos abundantes
- Herramientas de desarrollo más pesadas (Visual Studio)

## Alternativa 4: Desarrollo Nativo (Kotlin/Swift)

### Pros:
- Máximo rendimiento posible optimizado para cada plataforma
- Acceso inmediato a todas las características más recientes del sistema operativo
- Mejor integración con el ecosistema de cada plataforma
- Herramientas oficiales y soporte completo de Apple y Google
- Control total sobre comportamiento específico de plataforma

### Contras:
- Requiere desarrollar dos aplicaciones completamente separadas
- Necesita mantener dos bases de código diferentes
- Requiere dos equipos especializados o desarrolladores con doble experiencia
- Duplicación de esfuerzo para cada característica implementada
- Mayor tiempo de desarrollo y costos significativamente más altos
- Dificultad para mantener consistencia entre plataformas

## Descripción:
Flutter es un framework de UI desarrollado por Google que permite crear aplicaciones compiladas nativamente desde una única base de código escrita en Dart. Flutter utiliza su propio motor de renderizado (Skia) que dibuja cada píxel en pantalla, garantizando consistencia visual perfecta entre plataformas. Proporciona un rico catálogo de widgets prediseñados que siguen las guías Material Design (Android) y Cupertino (iOS), permitiendo crear interfaces que se sienten nativas en cada plataforma. El framework incluye gestión de estado integrada, navegación, animaciones, y acceso a características nativas mediante canales de plataforma. Dart, el lenguaje de programación de Flutter, es orientado a objetos, fuertemente tipado, y compila a código nativo de ARM para móviles, ofreciendo excelente rendimiento.

## Razón de la Decisión:
Flutter fue seleccionado como framework frontend porque proporciona el mejor balance entre eficiencia de desarrollo, rendimiento y capacidad de personalización para crear una interfaz accesible destinada a personas mayores. La compilación nativa garantiza fluidez en navegación y animaciones, aspecto crucial para una experiencia de usuario profesional. El extenso catálogo de widgets de Flutter permite construir interfaces altamente personalizadas con texto grande, colores de alto contraste, botones amplios y navegación simplificada, todos requisitos esenciales para accesibilidad de adultos mayores. La arquitectura de widgets declarativos facilita crear y mantener interfaces complejas de forma organizada. El soporte nativo para notificaciones locales mediante flutter_local_notifications es robusto y confiable, fundamental para la funcionalidad crítica de recordatorios médicos. Hot Reload acelera dramáticamente el desarrollo al permitir ver cambios instantáneamente sin reiniciar la aplicación. La capacidad de compartir código entre Android e iOS reduce el tiempo de desarrollo a la mitad comparado con desarrollo nativo, mientras mantiene rendimiento cercano al nativo. La comunidad Flutter es extremadamente activa con abundante documentación, paquetes disponibles y recursos de aprendizaje, facilitando resolver problemas y implementar funcionalidades complejas.