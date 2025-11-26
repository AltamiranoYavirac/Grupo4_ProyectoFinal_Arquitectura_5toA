# ADR 016: Infraestructura y Despliegue (Cloud-Native)

**Autores:** Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

**Asignatura:** Arquitectura de Software | 5 DS “A”

**Fecha:** 22–11–2025

**Estado:** Aceptado

# Matriz de Arquitectura Cloud
Resumen de Decisiones Tecnológicas

Esta matriz describe y justifica la elección de cómputo, datos y estrategia de despliegue para la aplicación orientada a adultos mayores que gestiona medicinas, citas y recordatorios, con énfasis en el funcionamiento offline-first, seguridad y bajo costo.

## Matriz de Selección Tecnológica

| **Categoría**                     | **Tecnología Seleccionada**                                     | **Justificación Técnica y de Negocio**                                                                                                                                                                                                                                                                                                                                                                              |
| --------------------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Cómputo**                       | **Serverless (AWS Lambda / Cloud Functions / Azure Functions)** | Elegido por su **bajo costo**, **escalado automático**, cero mantenimiento de servidores y alta disponibilidad. El backend solo se activa en sincronizaciones, por lo que pagar por ejecución es más eficiente que mantener un servidor encendido 24/7. Reduce la carga operativa y es ideal para una app que funciona principalmente **offline**, activando la nube solo cuando sea necesario.                     |
| **Base de Datos (Cliente)**       | **SQLite (en el dispositivo móvil)**                            | SQLite es la mejor opción para aplicaciones offline-first: es **rápida**, **liviana**, **embebida**, sin costos de servidor. Permite almacenar medicinas, recordatorios y citas sin depender de internet. También es ideal para usuarios adultos mayores, ya que garantiza funcionamiento incluso sin conectividad. Además, facilita una sincronización eficiente: solo se envían cambios incrementales al backend. |
| **Base de Datos (Nube)**          | **SQLite ligera / o SQL gestionado minimalista**                | Para la nube solo se requiere almacenamiento de respaldo y sincronización. Puede mantenerse SQLite en contenedor/función o migrar a un SQL gestionado básico. Mantiene costos bajos y es suficiente para almacenar copias sincronizadas sin tráfico intensivo.                                                                                                                                                      |
| **Almacenamiento Complementario** | **Object Storage (S3 / Cloud Storage)**                         | Permite guardar respaldos, logs y exportaciones de la base en un servicio muy económico y duradero (11 nueves de disponibilidad). Útil para auditoría o restauración del usuario.                                                                                                                                                                                                                                   |
| **Estrategia de Despliegue**      | **Canary Deployment (Elegido)**                                 | Minimiza el riesgo al desplegar nuevas versiones del backend. Con canary solo un **pequeño porcentaje** de usuarios usa la nueva versión primero. Si falla, se revierte sin impacto masivo. Es la opción de **menor costo y menor riesgo**, especialmente adecuada para apps médicas donde un error puede afectar recordatorios o medicación.                                                                       |
| **Seguridad**                     | **Autenticación gestionada (Cognito / Firebase Auth)** + TLS    | Evita que el equipo tenga que gestionar credenciales o contraseñas. TLS en todas las comunicaciones y cifrado de datos antes de sincronizar garantiza confidencialidad para información sensible de salud.                                                                              
 ---
## Cuadro Comparativo de Arquitecturas
| Criterio                              | Máquinas Virtuales           | Contenedores                           | Serverless (Elegida)            |
| ------------------------------------- | ---------------------------- | -------------------------------------- | ------------------------------- |
| **Costo**                             | Alto (pago continuo)         | Medio (según cluster)                  | Muy bajo (pago por ejecución)   |
| **Complejidad Operativa**             | Alta                         | Media                                  | Muy baja                        |
| **Escalabilidad**                     | Manual                       | Automática, con configuración compleja | Automática por defecto          |
| **Fiabilidad**                        | Depende de configuración     | Alta en clusters bien gestionados      | Muy alta (autoscaling regional) |
| **Seguridad**                         | Administrada por el equipo   | Depende del cluster                    | Gestionada por el proveedor     |
| **Rendimiento**                       | Alto, pero sobredimensionado | Alto                                   | Suficiente y escalable          |
| **Compatibilidad con la app offline** | ❌ No                         | ❌ No                                   | ✔ Sí                            |
| **Mantenimiento**                     | Alto                         | Medio                                  | Muy bajo                        |
| **Ideal para proyectos pequeños**     | ❌                            | ❌                                      | ✔                               |

 ---
### Justificación (Por qué Serverless (y no VM o Contenedores))

- No requiere administrar infraestructura.

- Se paga solo cuando la app sincroniza.

- Autoescalado sin impacto operativo.

- Perfecto para cargas intermitentes.

- Ideal para un proyecto móvil que NO depende de un backend continuo.

### Por qué SQLite como base principal

- Funciona sin internet.

- Simplicidad extrema y rendimiento muy alto en móvil.

- No requiere servidor.

- Flexible para sincronización incremental.

- Adecuado para adultos mayores que necesitan confiabilidad sin red.

--- 
### Estrategia de Despliegue Recomendada
Aunque una arquitectura Serverless no opere con servidores tradicionales, sigue siendo necesario definir una estrategia de despliegue para garantizar actualizaciones seguras y controladas.

### Por qué Canary Deployment

- No duplica infraestructura → menor costo.

- Evita que fallos afecten a todos los usuarios.

- Perfecto para backend pequeño o serverless.

- Pruebas controladas con tráfico real.

### Estrategia Elegida: Canary Deployment

- El despliegue canario permite liberar una nueva versión de una función o servicio a un pequeño porcentaje de usuarios antes de expandirla al resto. Esta estrategia es ideal para servicios Serverless, ya que:

- Minimiza riesgos al introducir cambios gradualmente.

- Permite detectar errores o comportamientos inesperados sin afectar a toda la base de usuarios.

- Resulta especialmente valiosa en aplicaciones relacionadas con recordatorios médicos, donde un error podría comprometer la salud del usuario.

**Ejemplo de despliegue en etapas**

1. Se lanza una nueva versión del sistema de notificaciones.

2. Solo el 5–10% de los usuarios comienza a utilizarla.

3. Si no se detectan errores, se amplía al 50%.

4. Finalmente, se publica al 100% de los usuarios.
---

```

                                 ┌───────────────────────────────┐
                                 │        Cliente (Móvil)         │
                                 │───────────────────────────────│
                                 │  App Móvil (Flutter)           │
                                 │  - SQLite                      │
                                 │  - Notificaciones locales      │
                                 └───────────────┬────────────────┘
                                                 │
                                                 │ HTTPS (sync/push, sync/pull)
                                                 ▼
                                   ┌───────────────────────────┐
                                   │     Internet / HTTPS      │
                                   └───────────────┬───────────┘
                                                   │
                                                   ▼
      ┌──────────────────────────────────────────────────────────────────────────────┐
      │                      REGIÓN CLOUD (ej. us-east-1)                            │
      │──────────────────────────────────────────────────────────────────────────────│

      │  ┌───────────────────────────────┐      ┌────────────────────────────────┐   │
      │  │      Public Subnet / Edge     │      │        Funciones Serverless    │   │
      │  │───────────────────────────────│      │────────────────────────────────│   │
      │  │  CDN (CloudFront)             │      │  Lambda v1                     │   │
      │  │  API Gateway / LB             │─────▶│  Lambda v2 (Canary)            │   │
      │  └───────────────────────────────┘      │  Scheduler (Backups)           │   │
      │                                          └────────────────────────────────┘   │
      │
      │  ┌───────────────────────────────┐      ┌────────────────────────────────┐   │
      │  │        Auth & Push            │      │            Datos / Storage      │   │
      │  │───────────────────────────────│      │────────────────────────────────│   │
      │  │  Auth Service                 │◀────▶│  NoSQL DB (DynamoDB/Firestore) │   │
      │  │  Push Service (FCM/APNs)      │◀────▶│  Object Storage (S3)           │   │
      │  │                               │      │  Cache Redis (Opcional)        │   │
      │  └───────────────────────────────┘      └────────────────────────────────┘   │
      │
      │  ┌───────────────────────────────┐
      │  │        Observabilidad         │
      │  │───────────────────────────────│
      │  │  Logs (CloudWatch/Stackdriver)│
      │  │  Alertas & Dashboards         │
      │  └───────────────────────────────┘
      │
      └──────────────────────────────────────────────────────────────────────────────┘

                 ▲
                 │ Push Notificaciones
                 └───────────────────────────────── Push Service (FCM/APNs)


```