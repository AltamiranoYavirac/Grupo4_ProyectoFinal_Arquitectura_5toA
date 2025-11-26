# ADR 010: Evolución Arquitectónica de Monolito a Microkernel

*Autores:* Jiménez D. Christopher, Altamirano Cristian, Iza Jordan

*Asignatura:* Arquitectura de Software | 5 DS “A”

### Fecha:
2025-10-20

### Estado:
Aceptado

---

## 🧩 Contexto

La aplicación de recordatorios médicos para personas mayores se ha diseñado inicialmente con una arquitectura **monolítica local** basada en **Flutter**, **SQLite** y **notificaciones locales**.  
Esta arquitectura cumple efectivamente con los requisitos actuales:  
- Funcionamiento offline  
- Privacidad de datos  
- Simplicidad operativa  
- Bajo costo de mantenimiento  

Sin embargo, se anticipa que en el futuro el sistema requerirá capacidades adicionales que pueden beneficiarse de extensibilidad y modularidad:

- Integración opcional con dispositivos *wearables* (smartwatches, tensiómetros, glucómetros)  
- Módulos de análisis de adherencia personalizables según condiciones médicas  
- Plugins de exportación a diferentes formatos (PDF, CSV, HL7 FHIR)  
- Extensiones para idiomas y dialectos regionales  
- Integraciones opcionales con farmacias locales o consultorios médicos  
- Módulos especializados para diferentes condiciones (diabetes, hipertensión, etc.)  
- Temas y *layouts* personalizables para diferentes niveles de discapacidad visual  

La arquitectura **Microkernel (Plugin Architecture)** permite mantener un núcleo simple y estable mientras se añaden funcionalidades mediante *plugins* independientes, evitando la complejidad y costos de microservicios distribuidos.

---

## ⚙️ Decisión

Adoptar una estrategia de **evolución arquitectónica hacia Microkernel** desde el monolito actual, manteniendo el funcionamiento *offline-first* y agregando extensibilidad mediante *plugins*:

### **Fase 1: Refactorización a Núcleo Modular (Actual - 6 meses)**
- Identificar y extraer el **Core System** (funcionalidad esencial mínima)  
- Definir **interfaces y contratos claros** para extensibilidad  
- Implementar **Plugin Registry** para descubrimiento y gestión de *plugins*  
- Establecer **Event Bus** interno para comunicación entre núcleo y *plugins*  
- Mantener toda la funcionalidad actual como parte del núcleo  
- Diseñar una **API de *plugins*** bien documentada  

### **Fase 2: Primer Conjunto de Plugins Internos (6–12 meses)**
Migrar funcionalidades no esenciales a *plugins* internos:

- **Plugin de Estadísticas:** Análisis de adherencia y reportes  
- **Plugin de Exportación:** Generación de PDFs y reportes médicos  
- **Plugin de Recordatorios Avanzados:** Patrones complejos de medicación  
- **Plugin de Temas:** Personalización visual y accesibilidad  

Los *plugins* se distribuyen con la aplicación pero son modulares internamente.  
Validar la arquitectura con casos de uso reales, sin costos adicionales de infraestructura.

### **Fase 3: Plugins Opcionales Descargables (12–18 meses)**
- Implementar **Plugin Marketplace** local (dentro de la app)  
- Desarrollar *plugins* opcionales instalables:
  - **Plugin de Wearables:** Integración con dispositivos IoT médicos  
  - **Plugin de Farmacias:** Conexión con farmacias locales  
  - **Plugin de Condiciones Específicas:** Diabetes, cardiología, etc.  
  - **Plugin de Sincronización Cloud:** Backup opcional cifrado  
  - **Plugin de Compartir con Familia:** Notificaciones a cuidadores  

Los *plugins* se descargan e instalan bajo demanda.  
El núcleo permanece ligero y simple.

### **Fase 4: Ecosistema de Plugins Abierto (18+ meses)**
- Abrir el **SDK de plugins** a desarrolladores terceros (opcional)  
- Permitir *plugins* desarrollados por la comunidad médica  
- Sistema de **revisión y aprobación** de *plugins*  
- *Plugins premium* opcionales como modelo de monetización  
- Mantener núcleo **open-source**, *plugins* pueden ser comerciales  
- Arquitectura completamente *offline* con *plugins* locales  

---

## ✅ Consecuencias

### **Positivas**
- Sencillez arquitectónica sin complejidad de sistemas distribuidos  
- Cero costos de infraestructura *cloud* (todo permanece local)  
- Extensibilidad infinita mediante *plugins*  
- Núcleo estable y mínimo  
- Personalización por usuario  
- Rendimiento óptimo (sin latencia de red)  
- Privacidad garantizada (datos locales)  
- Bajo acoplamiento entre núcleo y *plugins*  
- *Testing* simplificado (núcleo y *plugins* independientes)  
- Monetización flexible (*plugins premium*)  
- Comunidad participativa y extensible  

### **Negativas**
- Gestión de versiones entre núcleo y *plugins* requiere cuidado  
- Calidad variable en *plugins* de terceros  
- Necesidad de *sandboxing* para aislamiento  
- Documentación exhaustiva del SDK de *plugins*  
- Proceso de aprobación consume recursos  
- Fragmentación por combinaciones de *plugins*  
- Curva de aprendizaje para nuevos desarrolladores  

---

## 🔄 Alternativas a Considerar

### **Alternativa 1: Microservicios (Ya analizada previamente)**

**Pros:**
- Escalabilidad horizontal por servicio  
- Diferentes tecnologías por servicio  
- Equipos independientes  
- Resiliencia mediante aislamiento  

**Contras:**
- Costos operativos altos (servidores, orquestación)  
- Requiere conexión a internet constante  
- Complejidad de *debugging* distribuido  
- Latencia de red  
- No cumple con *offline-first*  
- *Over-engineering* para el tamaño del proyecto  

---

### **Alternativa 2: Monolito Modular Simple**

**Pros:**
- Máxima simplicidad  
- Sin complejidad de *plugins*  
- Código directo y fácil de entender  
- *Testing* simple  

**Contras:**
- Sin posibilidad de extender sin modificar el núcleo  
- Aplicación pesada (todas las funciones incluidas)  
- Difícil personalización por usuario  
- Cada cambio requiere nueva versión  
- Sin separación entre esencial y opcional  

---

### **Alternativa 3: Aplicación con Server-Side Plugins**

**Pros:**
- *Plugins* ejecutados en servidor  
- Mayor control de calidad  
- Actualizaciones centralizadas  

**Contras:**
- Requiere infraestructura backend  
- Dependencia de conexión a internet  
- Latencia en ejecución  
- Viola principio *offline-first*  
- Riesgos de privacidad (procesamiento en servidor)  

---

## 🧾 Conclusión

La evolución hacia una arquitectura **Microkernel** ofrece un camino equilibrado entre simplicidad, extensibilidad y privacidad.  
Permite mantener el enfoque *offline-first*, reducir costos operativos y crear un ecosistema modular adaptable a las necesidades futuras de los usuarios.

---
