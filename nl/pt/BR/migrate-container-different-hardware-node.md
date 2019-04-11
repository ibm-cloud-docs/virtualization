---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:tip: .tip}
{:new_window: target="_blank"}

# Migrando um contêiner para um nó de hardware diferente
{: #migrating-a-container-to-a-different-hardware-node}

1. Certifique-se de ter os dois nós de hardware configurados no PIM.
2. Acesse **Infraestrutura** e selecione o nó de hardware no qual o contêiner está.
3. Selecione o contêiner e clique em **Migrar**.
4. Selecione o **Nó de destino**.
5. Escolha se você deseja que a migração seja executada em tempo real.

**Nota:** dependendo do tamanho do contêiner, uma migração em tempo real pode não ser viável. A maioria das informações nos contêineres precisa ser retida em RAM até que a migração seja concluída.

6. Em **Avançado**, é possível selecionar se deseja ou não remover os arquivos de contêineres que estão no nó de origem após a migração. Especifique não para iniciar automaticamente o contêiner no nó de destino ou forçar a migração. Forçar a migração poderá causar problemas se você tiver outro nó usando o mesmo IP. Outro problema de migração é que o nó de destino não tem os modelos de aplicativo ou de S.O. corretos que estão instalados e armazenados em cache.
7. É possível visualizar os **Detalhes** para monitorar o processo de migração.
8. A migração está concluída. O contêiner é movido automaticamente para o nó de destino, o que pode ser localizado em **Infraestrutura** na área de janela de menu à esquerda.
