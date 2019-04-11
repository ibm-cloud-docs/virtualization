---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-23"

subcollection: virtualization

keywords: Windows 2008
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Instalación de la virtualización de Windows Server en Windows 2008
{: #installing-windows-server-virtualization-on-windows-2008}

<!--Windows 2008 64-bit edition comes with the option to install Windows next generation virtualization application codenamed Veridian. By default the application will not be a selectable option when Adding and Removing Roles from Windows 2008.

Note: At this time, this is only available in full installation of 64-bit editions of Windows 2008. 32-bit versions and Server-core installations do not support this.-->

## Antes de empezar
{: #before-you-begin-win-2008}

Antes de empezar, revise los siguientes requisitos previos.

### Hardware
{: #hardware}

* Tecnología de virtualización asistida por hardware habilitada en la BIOS
* Intel VT
* AMD-V
* Prevención de ejecución de datos habilitada en la BIOS
* Intel Execute Disable (XD)
* AMD No Execute (NX)

### Software
{: #software}

Para habilitar la opción de **Rol** para la virtualización de Windows Server, es necesario instalar algunos parches.
1. Abra una ventana de navegador y vaya a %sysdir%\Windows\wsv, normalmente C:\Windows\wsv. En dicha carpeta existirán dos archivos:
    * Windows6.0-KB939853-x64.msu
    * Windows6.0-KB939854-x64.msu
2. Estos parches se pueden instalar en cualquier orden. Instale los dos parches y, a continuación, reinicie el sistema.

## Instalación
{: #installation}

1. Después de que se reinicie el sistema, debe añadir el **Rol** al sistema.
2. Tras añadir el rol, aparecerá el recuadro de diálogo **Crear redes virtuales**.
**Nota:** se perderá brevemente la conectividad de red.
3. Seleccione **Conexión de área local**, su adaptador de red privado. Pulse **Continuar**. La instalación continúa y requiere que reinicie.
4. Una vez reiniciado el sistema, inicie sesión en el sistema a través de la conexión pública. **Nota:** debe iniciar sesión con la misma credencial de inicio de sesión que haya utilizado para instalar esta conexión. Perderá la conectividad de red con la interfaz. Si no es así, es posible que reciba el error siguiente:
    * *Ha fallado el intento de configurar la virtualización de Windows Server con código de error 0x80078000.*
Para resolver el error, vaya a **Inicio > Programas > Herramientas administrativas > Gestión de la virtualización de Windows** en la consola de gestión de la virtualización de Windows.
5. En el panel de la derecha, pulse sobre el servidor adecuado.
6. A continuación, en el panel de acción, pulse **Gestor de redes virtuales**. Ahora verá **Gestión de conmutadores de redes virtuales**.
7. En el panel de la izquierda, pulse sobre el conmutador de red en **Añadir nuevo** conmutador de red.
8. Renombre el conmutador de red a "privado".
9. Seleccione el adaptador de red físico para **Conexión** y seleccione el primer adaptador de red. Todos los protocolos de red están ahora desvinculados de la interfaz de red privada.
10. **IMPORTANTE**: para volver a establecer la conectividad de red en privada, necesita configurar el nuevo dispositivo de conmutación de manera que **NO** sea la interfaz privada. Vaya a **Inicio > Configuración > Conexiones de red**. Verá un nuevo dispositivo denominado **Conexión de área local 2**.
11. Pulse el botón derecho del ratón sobre esta conexión y vaya a **Propiedades**.
12. Seleccione **ipv4** y sus propiedades. Debe configurar este dispositivo con la dirección IP de interfaz de red privada, la máscara de red y los servidores DNS.
13. Pulse **Aceptar > Cerrar**. Esta configuración vuelve a habilitar la red en la parte privada. Puede verificar que la red es privada haciendo ping a la IP privada.

Utilice RDP con la IP privada para configurar la red pública.

### Adición de un conmutador público
{: #adding-a-public-switch}

La adición de un conmutador público sigue el mismo proceso que la adición de un conmutador privado.
1. Vuelva a **Gestión de conmutadores de redes virtuales** y seleccione **Añadir nuevo conmutador de red**.
2. Seleccione **Externo** como tipo de conmutador de red y pulse **Añadir**.
3. Renombre el conmutador a **Público** y seleccione **Adaptador de red físico**.
4. Seleccione el segundo adaptador de red y pulse **Aceptar**. Esta configuración provoca que el puerto público no responda en la red. Configure la nueva interfaz de conmutador público igual que lo hizo con el privado con sus propios valores.
