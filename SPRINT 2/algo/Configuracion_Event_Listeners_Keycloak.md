# Configuración de Event Listeners en Keycloak para Auditoría

**N°:** GRUPO 8  
**Integrantes:** Javier Vericat - Bryan Aguilera - Giuseppe Suarez  
**Profesores:** Sergi - David Sicart

---

## Índice

1. [Configuración de Keycloak](#1-configuración-de-keycloak)
   - [a. Activación de Event Listeners](#a-activación-de-event-listeners)
   - [b. Persistencia de Eventos de Usuario y Administración](#b-persistencia-de-eventos-de-usuario-y-administración)
2. [Configuración de Loki](#2-configuración-de-loki)
   - [a. Optimización del Dashboard - OverView](#a-optimización-del-dashboard---overview)

---

## 1. Configuración de Keycloak

### a. Activación de Event Listeners

Desde el Keycloak nos dirigiremos al apartado de **Events**, y seguidamente al apartado de **Event Config**.

> 📸 *Captura: Panel de Events en Keycloak mostrando el listado de User events con tipos como `CODE_TO_TOKEN`, `LOGIN`, `LOGIN_ERROR`, `UPDATE_PROFILE`, con sus respectivas marcas de tiempo, usuario, IP y cliente.*

---

### b. Persistencia de Eventos de Usuario y Administración

Para garantizar una trazabilidad completa, se han configurado los niveles de persistencia en el mismo panel de **Event Config**.

Actualmente tenemos estos dos oyentes de eventos:

- El componente principal es **`jboss-logging`**, que permite que Loki recolecte estos logs.
- El listener **`mail`** se mantiene como opcional.

> 📸 *Captura: Panel de Realm Settings > Events > Event listeners con los listeners `jboss-logging` y `mail` configurados.*

---

Podemos ver que ya lo tenemos configurado:

> 📸 *Captura: Pestaña "User events settings" con las siguientes opciones activas:*
> - **Save events:** On
> - **Expiration:** 20 Days
>
> *Tipos de eventos guardados:*
> - Update consent error
> - Send reset password
> - Grant consent
> - Verify profile error
> - Update totp
> - Remove totp

---

Por el apartado de **Admin** también:

> 📸 *Captura: Pestaña "Admin events settings" con las siguientes opciones activas:*
> - **Save events:** On
> - **Include representation:** On
> - **Expiration:** 20 Days

---

## 2. Configuración de Loki

### a. Optimización del Dashboard - OverView

Ahora tenemos que editar nuestro dashboard, específicamente el dashboard llamado **alertas de intrusión**.

Hemos tenido que realizar diferentes configuraciones.

El principal es en el apartado de **code**, hemos ingresado lo siguiente:

```logql
sum(count_over_time({job=~".+"} |= "Failed" [1h])) or vector(0)
+
sum(count_over_time({job=~".+"} |= "LOGIN_ERROR" [1h])) or vector(0)
```

Esto lo hemos realizado con `or vector(0)`, para evitar que no muestre nada cuando no haya inactividad. Unimos dos tipos de eventos: los de autenticación del sistema (**SSH**) y los de la aplicación (**Keycloak**) en una sola métrica global.

Y de nombre hemos cambiado a:

> **Alertas de Seguridad y Acceso**

> 📸 *Captura: Editor del panel en Grafana mostrando el panel "Alertas de Seguridad y Acceso" con valor **1473**, con la query LogQL ingresada y el tipo de visualización configurado como "Stat".*

---

Ahora nuestro Panel de **OverView** quedaría de la siguiente manera:

> 📸 *Captura: Dashboard Overview en Grafana mostrando:*
> - Panel "Carga Actual de CPU" con gráfica temporal
> - Panel "Actividad de Red" mostrando **42.0 b/s**
> - Panel **"Alertas de Seguridad y Acceso"** mostrando **1266**

Finalmente, el nombre del panel se ha actualizado a **Alertas de Seguridad y Acceso**.

Por otro lado, la visualización de los datos confirma que el sistema está procesando correctamente los logs históricos desde el inicio del servicio de Keycloak, validando la **integración total del sistema de auditoría**.
