---
copyright:
  years: 2014, 2018
lastupdated: "2018-05-14"

subcollection: virtualization

keywords: Virtuozzo
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Fazendo backup de um contêiner do Virtuozzo
{: #backing-up-a-virtuozzo-container}

1. Certifique-se de que ambos os nós de hardware estejam configurados no PIM.
2. Acesse os nós de infraestrutura e de hardware nos quais o contêiner está.
3. Selecione o contêiner apropriado e clique em **Fazer backup**.
4. Inclua um comentário apropriado que descreva o motivo para fazer backup desses dados.
5. Selecione o tipo de backup:
   * Usar configurações do nó de hardware
   * Integral (para este exemplo)
   * Incremental
   * Diferencial
6. Selecione o Nível de compactação: **Nota:** a compactação mais alta pode economizar espaço em disco, mas usa mais recursos de CPU
   * Usar configurações do nó de hardware
   * Nenhum
   * Normal (escolha o carregamento do servidor mínimo normal)
   * Alta
   * Máximo
7. Selecione o nó de hardware no qual você deseja que o arquivo de backup seja hospedado. Se você tiver múltiplos nós de hardware, é aconselhável que você salve seus backups nesses nós, em vez de no mesmo servidor.
  * Outras opções estão na digressão do administrador.
8. Backup. O backup está completo. É possível visualizar os detalhes do backup acessando a guia **Nó de hardware, Contêiner e Backups** e selecionando o backup.
