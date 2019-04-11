---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Agrupación de XenServer
{: #xenserver-pooling}

La agrupación de XenServer puede estar pensada para hasta 16 servidores que trabajen como un clúster. Este clúster no solo permite recursos compartidos, sino también la migración en directo de máquinas virtuales que utilizan XenMotion. Un servidor actúa como "nodo maestro", mientras que el resto son esclavos. Al abrir cualquiera de los servidores de la agrupación a través de XenCenter, se abre la información de gestión de la agrupación completa. Cuando un nodo se une a la agrupación, se cambia su contraseña de gestión para que coincida con la contraseña de gestión del nodo maestro. Al eliminar un nodo de la agrupación, no se puede cambiar esta contraseña.

Todos los sistemas del nodo deben ser de la misma familia de procesadores, ya sea Intel o AMD. Idealmente, el hardware debe ser idéntico para que la agrupación tenga éxito. No es totalmente cierto que existan problemas de compatibilidad con agrupaciones de CPU heterogéneas. Es necesario realizar pruebas de compatibilidad para comprobar si el hardware es compatible o no. Para obtener más información, consulte
[Explicación de las agrupaciones de CPU heterogéneas
![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](http://support.citrix.com/article/CTX127059){: new_window}.

Tiene dos métodos para crear una agrupación de XenServer. Uno es a través de la consola de XenCenter mediante el uso de la opción "Nueva agrupación". Siga las instrucciones detalladas en pantalla paso a paso para crear y añadir sistemas a la agrupación. El otro método es mediante el uso de la línea de mandatos. En el nuevo nodo esclavo, se puede ejecutar el mandato siguiente:

        `xe pool-join master-address= master-username= master-password=`

**Nota**: la dirección IP de gestión es la dirección IP 10.x.x.x del servidor si ha utilizado la instalación predeterminada.

Al trabajar con redes vinculadas, es posible que las interfaces de un esclavo recién incorporado se conecten a las redes vinculadas incorrectas. Para corregir este problema, destruya los vínculos en el esclavo desde la línea de mandatos y vuelva a crear los vínculos con las redes y PIF adecuados.

Existen dos métodos para expulsar un nodo esclavo de la agrupación. El primero es utilizando XenCenter. Vaya a **Agrupación** y seleccione **Eliminar servidor**. Siga las instrucciones detalladas en pantalla paso a paso que se proporcionan. El otro método es mediante el uso de la línea de mandatos. La eliminación de un nodo se puede realizar desde cualquier nodo de la agrupación. Necesita localizar el
_uuid_ del nodo que desee expulsar ejecutando el mandato siguiente:

        `xe host-list`

A continuación, ejecute este mandato:

        `xe pool-eject uuid=`

**Nota**: cuando se expulse un nodo, se rearrancará en una instalación limpia, que incluye la eliminación de la configuración de red que ha utilizado para proporcionar la conectividad pública y privada. La contraseña del nodo esclavo sigue siendo la misma que la del nodo maestro después de su expulsión de la agrupación. Es posible que tenga que volver a configurar la red a través de IPMI, ya que la eliminación puede dañar la configuración de los esclavos.
