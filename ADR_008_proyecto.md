# ADR 008: Implementación del Servicio de Notificaciones

### Fecha:
2025-10-20

### Estado:
Aceptado

## Contexto:

El sistema de recordatorios médicos depende de notificaciones locales para alertar al usuario en horarios específicos sobre medicinas o citas. La solución debe funcionar de forma confiable incluso con la aplicación cerrada o el dispositivo bloqueado, y debe ser independiente de conexión a internet.

## Decisión:

Implementar las notificaciones utilizando los paquetes:

flutter_local_notifications para mostrar notificaciones locales,

android_alarm_manager_plus para programar alarmas en segundo plano (Android),

Integración con el sistema operativo móvil (Android/iOS) para ejecutar recordatorios exactos.

## Consecuencias:

Permite mostrar notificaciones precisas sin conexión.

El sistema operativo garantiza la entrega incluso si la app no está activa.

Se requiere manejo de permisos en ambas plataformas.

En iOS, algunas limitaciones de fondo pueden requerir configuraciones adicionales.

La lógica de programación de alarmas debe sincronizarse con los datos almacenados en SQLite.

# Alternativas Consideradas:

## Alternativa 1: Firebase Cloud Messaging (FCM)
### Pros:

Soporte multiplataforma nativo.

Permite notificaciones remotas y sincronización entre dispositivos.

## Contras:

Requiere conexión a internet.

Introduce dependencias de servicios externos (Google).

Compromete la privacidad de los datos médicos.

Alternativa 2: WorkManager / BackgroundFetch

## Pros:

Permite ejecutar tareas periódicas sin necesidad de alarmas.

Adecuado para sincronizaciones o limpiezas.

## Contras:

No garantiza exactitud de tiempo (no apto para alarmas médicas).

No muestra notificaciones inmediatas sin proceso activo.

## Descripción:

El servicio se implementa como un módulo independiente que:

Lee los horarios de medicinas y citas desde la base de datos local.

Programa notificaciones usando flutter_local_notifications.

Utiliza android_alarm_manager_plus para ejecutar tareas programadas incluso si la app está cerrada.

Al dispararse la alarma, genera la notificación con título, mensaje y acción (por ejemplo, “Tomar medicamento X”).

## Razón de la Decisión:

Esta combinación de paquetes ofrece una solución completamente offline, robusta y multiplataforma. La independencia de la red es esencial para personas mayores que pueden no tener conexión constante. Las notificaciones locales garantizan la entrega exacta y confiable, mientras que el uso de alarmas del sistema permite funcionamiento continuo sin intervención del usuario.