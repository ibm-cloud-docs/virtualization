---
copyright:
  years: 2014, 2018
lastupdated: "2018-08-16"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# ¿Qué es Virtuozzo?
{: #what-is-virtuozzo-}

Virtuozzo es una solución de virtualización basada en contenedores que permite compartir hardware utilizando una capa de abstracción.  Virtuozzo crea contenedores, también conocidos como VE o VPS, que simulan un servidor. El contenedor actúa y responde principalmente como si fuera un servidor autónomo. El contenedor es independiente de otros contenedores que se encuentran en el mismo servidor físico, en el sentido en que no pueden acceder a otros archivos de contenedores, recursos de IPC ni memoria. La red se puede configurar para que se comparta entre varios contenedores o para que esté aislada.
{:shortdesc}

Virtuozzo no es un hipervisor ni una interfaz de software para un hipervisor. Opera en una mezcla entre el espacio de usuario y de kernel centro de un kernel propietario. Esta configuración permite básicamente que los contenedores puedan tomar recursos prestados cuando lo necesitan, mientras los recursos están disponibles en el nodo de host.

Para obtener más información en [Virtuozzo ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://virtuozzo.com/){: new_window}.
