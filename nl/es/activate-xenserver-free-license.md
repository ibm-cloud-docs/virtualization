---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-13"

subcollection: virtualization

keywords: Citrix XenServer 
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Activación de XenServer
{: #activating-xenserver}

La licencia gratuita de Citrix XenServer ofrece una licencia de 30 días desde la recarga o la instalación inicial. Durante este tiempo, necesita registrarse en Citrix y solicitar una licencia de 1 año completamente funcional. Para crear esta licencia, Citrix requiere información personal a la que {{site.data.keyword.cloud}} no tiene acceso. La licencia es un acuerdo entre usted y Citrix para el uso de XenServer.
{:shortdesc}

Si no registra su sistema en un plazo de 30 días, no podrá encender ninguna máquina virtual.
{:tip}

Para registrar el sistema, realice los pasos siguientes:

1. Descargue e instale el cliente XenCenter. Puede acceder al cliente desde la página de
[Citrix XenCenter ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://community.citrix.com/display/xs/XenCenter){: new_window}.

2. Conéctese a la red privada a través de VPN.

3. Inicie XenCenter y pulse **Añadir nuevo servidor**, o bien pulse **Servidor > Añadir...** en el menú desplegable.

4. Utilice la dirección IP privada para el **Nombre de host**, 'root' para el **Nombre de usuario** y la contraseña de root para **Contraseña**.

5. Después de que se conecte el sistema, aparecerá un recuadro de diálogo. Este recuadro de diálogo le indica que la licencia caduca en 30 días. Si no aparece ningún recuadro de diálogo, en el menú desplegable, vaya a **Herramientas > Gestor de licencias...**.

6. Compruebe el servidor y pulse **Activar...**. Se abrirá un navegador y accederá al sitio de registro de Citrix. Complete el formulario y envíelo. Compruebe su bandeja de entrada para ver si hay un correo electrónico de Citrix. Este correo electrónico contiene su clave de licencia como archivo adjunto.

7. Tras recibir el correo electrónico, guarde el archivo de clave de licencia adjunto en local.

8. En el panel de la izquierda de XenCenter, resalte el servidor. En la barra de menús, vaya a **Servidor > Instalar clave de licencia...**. Aparecerá un recuadro de diálogo que solicita la ubicación de la clave de licencia. Seleccione la ubicación guardada en el paso 7.
