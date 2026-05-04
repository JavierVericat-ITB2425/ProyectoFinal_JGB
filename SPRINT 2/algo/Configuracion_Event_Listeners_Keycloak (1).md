# **Configuración de Event Listeners en Keycloak para Auditoría**

![Portada](./img-000.png)

**N°:** GRUPO 8  
**Integrantes:** Javier Vericat - Bryan Aguilera - Giuseppe Suarez  
**Profesores:** Sergi - David Sicart

# **Índice**
- [1. Configuración de Keycloak](#1-configuración-de-keycloak)
  - [1.1 Activación de Event Listeners](#11-activación-de-event-listeners)
  - [1.2 Persistencia de Eventos de Usuario y Administración](#12-persistencia-de-eventos-de-usuario-y-administración)
- [2. Configuración de Loki](#2-configuración-de-loki)
  - [2.1 Optimización del Dashboard - OverView](#21-optimización-del-dashboard---overview)

---

# **Configuración de Event Listeners en Keycloak para Auditoría**

## **1. Configuración de Keycloak**

### **1.1 Activación de Event Listeners**

   Desde el Keycloak nos dirigiremos al apartado de **Events**, y seguidamente al apartado de **Event Config**.

   ![Configuración de Event Listeners](./img-001.png)

### **1.2 Persistencia de Eventos de Usuario y Administración**

   Para garantizar una trazabilidad completa, se han configurado los niveles de persistencia en el mismo panel de **Event Config**.

   - **Oyentes de Eventos**: Actualmente tenemos configurados dos oyentes de eventos principales.
   - **jboss-logging**: Es el componente principal que permite que **Loki** recolecte estos logs para su posterior análisis.
   - **mail**: Este listener se mantiene como opcional.

   ![Configuración de Eventos de Usuario](./img-002.png)
   *Configuración de la persistencia para eventos de usuario.*

   ![Configuración de Eventos de Administración](./img-003.png)
   *Configuración de la persistencia para eventos de administración.*

#### **Comprobación**

   - **Configuración de Usuario**: Podemos verificar que los oyentes ya están activos en el panel correspondiente.
   - **Apartado de Administración**: Se ha validado que los eventos de administración también están siendo registrados correctamente.

   ![Listado de Eventos en Keycloak](./img-004.png)

---

## **2. Configuración de Loki**

### **2.1 Optimización del Dashboard - OverView**

   Para visualizar correctamente las alertas, es necesario editar el dashboard específico llamado **Alertas de Intrusión**.

   Hemos realizado diferentes configuraciones en el apartado de **Code**, ingresando la siguiente consulta:

```logql
sum(count_over_time({job=~".+"} |= "Failed" [1h])) or vector(0)
+
sum(count_over_time({job=~".+"} |= "LOGIN_ERROR" [1h])) or vector(0)
```

   ![Edición de Dashboard en Grafana](./img-005.png)

   **Detalles de la configuración:**
   - **or vector(0)**: Se utiliza para asegurar que el panel muestre información incluso cuando no hay actividad (evitando que el gráfico desaparezca).
   - **Unificación**: Unimos dos tipos de eventos críticos:
     - Eventos de autenticación del sistema (**SSH**).
     - Eventos de la aplicación (**Keycloak**).
   - **Resultado**: Ambas fuentes se consolidan en una sola métrica global de seguridad.

#### **Comprobación**

   - **Nombre del Panel**: Se ha actualizado a **Alertas de Seguridad y Acceso**.
   - **Visualización**: El Panel de OverView ahora muestra correctamente los logs históricos desde el inicio del servicio de Keycloak.
   - **Validación**: Los datos confirman que el sistema está procesando correctamente la información, validando la integración total del sistema de auditoría.

   ![Panel OverView Final](./img-006.png)
