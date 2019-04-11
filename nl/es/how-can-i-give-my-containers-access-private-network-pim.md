---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Cómo proporcionar a los contenedores acceso a la red privada a través de PIM
{: #giving-containers-access-to-the-private-network-through-pim}

Puede proporcionar a los contenedores acceso a su red privada. Sin embargo, debe entender las implicaciones de proporcionar acceso a su infraestructura interna a los clientes, lo cual puede provocar problemas de seguridad.

1. Vaya al contenedor al que desee otorgar acceso a la red privada.
2. Pulse el separador **Red** y pulse **Configurar**.
3. Ahora puede configurar la sección de red de puente:
  * **Red de puente**: marque el recuadro de selección.
  * **Conectar a**: no es necesario para proporcionar acceso a la red privada de
{{site.data.keyword.cloud}}.
  * **Obtener dirección IP por DHCP**: deseleccione este recuadro de selección.
  * **Dirección IP / Máscara de subred**: elija una dirección IP interna de su agrupación de direcciones privadas.
  * Pulse **Enviar**

Ahora deberá renovar la página desde el enlace de la parte superior derecha y habrá terminado.
