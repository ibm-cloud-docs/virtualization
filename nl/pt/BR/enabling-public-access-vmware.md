---
copyright:
  years: 2014, 2018
lastupdated: "2018-01-11"

subcollection: virtualization
---
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Ativando o acesso público ao VMware
{: #enabling-public-access-to-vmware}

Por padrão, o host ESX é instalado com o console de serviço apenas na rede privada, o que significa que o tráfego público para e da rede pública é evitado no host ESX.

Para iniciar, é necessário conectar-se ao servidor usando um VMware vSphere Client na interface privada do servidor, seguindo estas etapas.

1. Clique em **Host ESX > Guia de configuração > Rede > Incluir rede**
2. Selecione o console de serviço.
3. Selecione um vSwitch a ser usado. Nesse caso, ele é as interfaces externas/públicas, portanto, selecione **vSwitch1** (vmnic1 e vmnic3).
4. Renomeie o console de serviço para algo que seja facilmente reconhecível (por exemplo, "Console de serviço público").
5. Insira o endereço IP externo primário que é designado para o servidor com a máscara de sub-rede correta.
6. Clique em **Editar** e inclua o gateway padrão que está designado para o servidor.
