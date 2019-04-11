---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migración de un contenedor a un nodo de hardware distinto
{: #migrating-a-container-to-a-different-hardware-node}

1. Asegúrese de que tiene ambos nodos de hardware configurados en PIM.
2. Vaya a **Infraestructura** y seleccione el nodo de hardware en el que se encuentra el contenedor.
3. Seleccione el contenedor y pulse **Migrar**.
4. Seleccione el **Nodo de destino**.
5. Elija si desea que la migración se realice en directo.

**Nota:** en función del tamaño del contenedor, es posible que una migración en directo no sea factible. La mayoría de la información de los contenedores debe estar en RAM hasta que finalice la migración.

6. En **Avanzado**, puede seleccionar si desea o no eliminar los archivos de los contenedores que se encuentran en el nodo de origen tras la migración. Especifique no iniciar automáticamente el contenedor en el nodo de destino ni forzar la migración. Al forzar la migración puede surgir problemas si tiene otro nodo que utiliza la misma IP. Otro problema de la migración es que el nodo de destino no tenga las plantillas de aplicación o SO correctas instaladas y en memoria caché.
7. Puede ver los **Detalles** para supervisar el proceso de migración.
8. La migración ha finalizado. El contenedor se moverá automáticamente al nodo de destino, que se puede encontrar en
**Infraestructura** en el panel de menú de la izquierda.
