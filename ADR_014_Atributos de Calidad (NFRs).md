# 1. Atributos de Calidad (NFRs)

Los atributos de calidad más relevantes para el sistema son aquellos que aseguran su confiabilidad, accesibilidad y protección de datos sensibles. Para esta aplicación dirigida a personas mayores y que maneja información médica, se seleccionan tres atributos críticos: Disponibilidad, Seguridad y Usabilidad.

## 1.1 Escenarios de Atributos de Calidad

A continuación se presentan tres escenarios usando el formato formal:
Fuente → Estímulo → Artefacto → Entorno → Respuesta → Medida de Respuesta.


---
### Atributo 1: Disponibilidad
---
Escenario Formal:

- Fuente: Usuario final (persona mayor).

- Estímulo: Intenta consultar su medicación o recibe una alarma programada.

- Artefacto: Aplicación móvil y módulo de recordatorios locales.

- Entorno: El dispositivo se encuentra sin conexión a internet.

- Respuesta: La aplicación debe funcionar de forma completa en modo offline, ejecutando alarmas, mostrando datos y permitiendo modificaciones.

- Medida de Respuesta:

Disponibilidad del sistema en modo offline: 100%.

Tolerancia máxima en alarmas: ± 2 segundos.

---
### Atributo 2: Seguridad
---
Escenario Formal:

- Fuente: Actor malintencionado o usuario sin autenticación válida.

- Estímulo: Intento de acceder a información médica almacenada localmente o en la nube.

- Artefacto: Base de datos local (SQLite) + autenticación en backend Serverless.

- Entorno: Sesión inválida o dispositivo bloqueado.

- Respuesta: El sistema debe proteger los datos mediante cifrado, autenticar la identidad del usuario y registrar accesos sospechosos.

- Medida de Respuesta:

Cifrado de datos sensibles con AES-256.

Autenticación obligatoria mediante JWT / OAuth2 / Firebase Auth.

Bloqueo automático después de 5 intentos fallidos.

---
### Atributo 3: Usabilidad (Accesibilidad)
---
Escenario Formal:

- Fuente: Usuario adulto mayor con bajo nivel tecnológico.

- Estímulo: Necesita agregar un medicamento o configurar una alarma.

- Artefacto: Interfaz gráfica de la aplicación móvil.

- Entorno: Uso normal del dispositivo sin asistencia externa.

- Respuesta: El sistema debe ser claro, simple, legible y de fácil navegación.

Medida de Respuesta:

Todas las funciones críticas disponibles en 1–2 toques.

Tamaño mínimo de letra: 16–18 pt.

Tasa de error de usuario menor a 5% en pruebas con adultos mayores.

---

### 2.2 Matriz de Trade-offs (Compensaciones)

| Decisión                                    | Beneficio                                          | Sacrificio                                | Justificación                                                                               |
| ------------------------------------------- | -------------------------------------------------- | ----------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Arquitectura Serverless**                 | Bajo costo, escalado automático, sin mantenimiento | Latencia ocasional por *cold start*       | La app opera mayormente offline; el backend no es crítico para el funcionamiento principal. |
| **Diseño Offline-first**                    | Garantiza disponibilidad total sin internet        | Sincronización más compleja               | El público objetivo puede no tener conexión frecuente.                                      |
| **Cifrado fuerte de datos**                 | Protección robusta de información médica           | Mayor consumo de recursos del dispositivo | La privacidad y seguridad son prioridades del sistema.                                      |
| **UI simplificada para adultos mayores**    | Alta accesibilidad y facilidad de uso              | Menos opciones avanzadas                  | La interfaz está optimizada para usuarios con menor experiencia tecnológica.                |
| **Arquitectura desacoplada y event-driven** | Mayor mantenibilidad y extensión futura            | Menor control secuencial                  | Permite integrar funciones futuras como analíticas o notificaciones inteligentes.           |


