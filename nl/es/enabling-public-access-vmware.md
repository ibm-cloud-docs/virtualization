---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Habilitación del acceso público a VMware
{: #enabling-public-access-to-vmware}

De forma predeterminada, el host ESX se instala con la consola de servicio únicamente en la red privada, lo que implica que se impide el tráfico público con la red pública en el host ESX.

Para empezar, necesitará conectarse al servidor utilizando un cliente VMware vSphere en la interfaz privada del servidor siguiendo estos pasos.

1. Pulse **Host ESX > Separador Configuración > Redes > Añadir red**
2. Seleccione la consola de servicio.
3. Seleccione un vSwitch a utilizar. En este caso, son las interfaces externa o pública, por lo que debe seleccionar
**vSwitch1** (vmnic1 y vmnic3).
4. Renombre la consola de servicio a algo fácilmente reconocible (por ejemplo, "Consola de servicio pública").
5. Especifique la dirección IP externa primaria asignada al servidor con la máscara de subred correcta.
6. Pulse **Editar** y añada la pasarela predeterminada asignada al servidor.
