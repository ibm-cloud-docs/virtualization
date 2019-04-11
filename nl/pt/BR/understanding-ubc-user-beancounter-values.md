---
copyright:
  years: 2014, 2018
lastupdated: "2018-11-15"

subcollection: virtualization
---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Entendendo os valores do contador de UBC/bean do usuário
{: #understanding-ubc-user-beancounter-values}

Use o Modo SLM se o UBC não for entendido. O SLM é mais fácil e fornece o mesmo tipo de controle.

|Nome|Tipo|Descrição|
|---|---|---|
|*Parâmetros principais*|||
|`numproc`|*sistema*|Número de processos e encadeamentos|
|`numtcpsock`|*sistema*|Número de soquetes TCP|
|`numothersock`|*sistema*|Número de soquetes diferentes de TCP|
|`vmguardpages`|*sistema*|Garantia de alocação de memória|
|`cpuunits`|*cpu*|Energia da CPU|
|`diskspace`|*disco*|Cota de espaço em disco|
|`rate`|*rede*|Largura da banda da rede|
|`ratebound`|*rede*|Se a largura da banda da rede for limitada|
|*Parâmetros adicionais*|||
|`kmemsize`|*sistema*|Tamanho da memória do kernel não trocável alocada para processos nesse contêiner|
|`tcpsndbuf`|*sistema*|Tamanho total de buffers de envio do TCP|
|`tcprcvbuf`|*sistema*|Tamanho total de buffers de recebimento do TCP|
|`othersockbuf`|*sistema*|Tamanho total de buffers de soquete do domínio UNIX, UDP e outros buffers de envio de protocolo de datagrama|
|`dgramrcvbuf`|*sistema*|Buffers de recebimento do UDP e outros protocolos de datagrama|
|`oomguardpages`|*sistema*|A quantia garantida de memória para o caso em que a memória esteja superlotada (garantia de eliminação de falta da memória)|
|`privvmpages`|*sistema*|Limite de alocação de memória|
|`lockedpages`|*sistema*|Páginas de processo não permitidas para troca (páginas bloqueadas por [mlock(2)![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://linux.die.net/man/2/mlock))|
|`shmpages`|*sistema*|Tamanho total de memória compartilhada (comunicação interprocessual ou IPC, mapeamentos anônimos compartilhados e objetos tmpfs) em páginas|
|`physpages`|*sistema*|Número total de páginas do RAM usadas por processos|
|`numfile`|*sistema*|Número de arquivos abertos|
|`numflock`|*sistema*|Número de bloqueios de arquivo|
|`numpty`|*sistema*|Número de pseudo terminais|
|`numsiginfo`|*sistema*|Número de estruturas siginfo|
|`dcachesize`|*sistema*|Tamanho total de estruturas dentry e inode bloqueadas na memória|
|`numiptent`|*sistema*|Número de entradas de NETFILTER (filtragem de pacote de IP)|
|`cpulimit`|*cpu*|Limite o consumo de CPU|
|`diskinodes`|*disco*|Cota de inode de disco (objeto filesystem)|
|`quotatime`|*disco*|Período de carência da cota|
|`quotaugidlimit`|*disco*|Limite no número de entradas de contabilidade UID e GID|
<caption>Tabela 1. Descrições de parâmetros do SLM</caption>

É possível acessar VzLinuxUBCMgmt.pdf ou visualizá-lo em HTML, no PIM em seu servidor, acessando:
**Gerenciamento > Suporte > Downloads > Gerenciamento do Guia do administrador de recursos do UBC**

Para obter mais informações sobre o UBC, consulte [OpenVZ Virtuozzo Containers, Categoria: UBC ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo")](http://wiki.openvz.org/Category:UBC).
**Nota:** nem tudo no OpenVZ é o mesmo que no Virtuozzo.
