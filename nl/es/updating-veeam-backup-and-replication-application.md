---
copyright:
  years: 2014, 2018
lastupdated: "2018-02-05"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Actualización de la aplicación de copia de seguridad y réplica de Veeam
{: #updating-veeam-backup-and-replication-application}

## Visión general
{: #overview}

Utilice los pasos siguientes para actualizar la aplicación de copia de seguridad y réplica de Veeam que se ejecuta en su servidor cuando estén disponibles nuevas actualizaciones del producto.

### Requisitos previos
{: #prerequisites-updating-veeam-backup-and-replication-application}

* Una cuenta gratuita de Veeam. Puede registrarse en
[Registro de Veeam ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://www.veeam.com/signin.html).
* Internet Explorer, Chrome, Firefox o Safari.

### Comprobación de actualizaciones
{: #checking-for-updates}

La aplicación de copia de seguridad y réplica de Veeam incluye un comprobador de actualizaciones. Para asegurarse de que se le notifique acerca de las actualizaciones, acceda al icono del menú de la parte superior izquierda y pulse **Opciones generales** >
**Notificaciones**. Asegúrese de que **Comprobar actualizaciones del producto y del hipervisor de forma periódica** esté seleccionado.

### Actualización de Veeam
{: #updating-veeam}

Para iniciar la actualización, siga estos pasos.
1. Abra la vista **Información de copia de seguridad**.
2. En el panel de inventario, vaya a **Nodo de servidores gestionados** > **Actualizaciones que faltan**.
3. Pulse sobre el enlace de actualización.

Algunas cosas a tener en cuenta antes de iniciar la actualización:

* Asegúrese de que finalice la ejecución actual de todos los trabajos existentes. Si algunos trabajos fallan, vuelva a ejecutar los trabajos que han fallado.
* Asegúrese de que no haya ningún trabajo en ejecución, incluyendo sesiones de restauración, sesiones de Instant VM Recovery ni trabajos
SureBackup. Se recomienda que no detenga los trabajos en ejecución.
* Inhabilite temporalmente los trabajos de copia de seguridad periódicos para evitar que se inicien durante la actualización.

### Pasos siguientes
{: #next-steps-updating-veeam-backup-and-replication-application}

Ahora puede extraer la actualización descargada y realice una doble pulsación sobre el instalador de la actualización de Veeam. La actualización se envía tanto al servidor Veeam local como a los hipervisores en los que está activo el agente Veeam. Cuando haya finalizado el proceso, inicie la copia de seguridad y réplica de Veeam y vuelva a habilitar los trabajos de copia de seguridad.
