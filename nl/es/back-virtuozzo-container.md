---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Copia de seguridad de un contenedor de Virtuozzo
{: #backing-up-a-virtuozzo-container}

1. Asegúrese de que ambos nodos de hardware estén configurados en PIM.
2. Acceda a los nodos de infraestructura y de hardware en los que se encuentra el contenedor.
3. Seleccione el contenedor adecuado y pulse **Realizar copia de seguridad**.
4. Añada un comentario adecuado que describa el motivo de realizar una copia de seguridad de estos datos.
5. Seleccione el tipo de copia de seguridad:
   * Utilizar valores de nodos de hardware
   * Completa (tipo de este ejemplo)
   * Incremental
   * Diferencial
6. Seleccione el nivel de compresión: **Nota:** una compresión mayor puede ahorrar espacio de disco, pero utiliza más recursos de CPU
   * Utilizar valores de nodos de hardware
   * Ninguna
   * Normal (elegir normal minimiza la carga del servidor)
   * Alta
   * Máxima
7. Seleccione el nodo de hardware donde desee alojar el archivo de copia de seguridad. Si tiene varios nodos de hardware, se recomienda que guarde las copias de seguridad en estos nodos, en lugar de hacerlo en el mismo servidor.
  * Las demás opciones siguen el criterio del administrador.
8. Copia de seguridad. La copia de seguridad se ha completado. Puede ver los detalles de la copia de seguridad en el separador
**Nodo de hardware, contenedor y copias de seguridad** al seleccionar la copia de seguridad.
