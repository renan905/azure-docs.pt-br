---
title: 'Início Rápido: Criar um balanceador de carga interno – CLI do Azure'
titleSuffix: Azure Load Balancer
description: Este guia de início rápido mostra como criar um balanceador de carga interno usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 834b5c3651a7fff085dc53096f66d5e3f4bf27b4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700402"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Início Rápido: Criar um balanceador de carga interno para balancear a carga de VMs usando a CLI do Azure

Veja uma introdução ao Azure Load Balancer usando a CLI do Azure para criar um balanceador de carga público e três máquinas virtuais.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Este guia de início rápido requer a versão 2.0.28 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

* **CreateIntLBQS-rg** nomeado. 
* Na localização **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**SKU Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar as VMs e o balanceador de carga, crie os recursos da rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* Chamada **myVNet**.
* Prefixo de endereço **10.1.0.0/16**.
* Sub-rede chamada **myBackendSubnet**.
* Prefixo de sub-rede **10.1.0.0/24**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Localização de **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um Standard Load Balancer, as VMs no endereço de back-end precisam ter adaptadores de rede que pertençam a um grupo de segurança de rede. 

Crie um grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Chamado **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Chamada **myNSGRuleHTTP**.
* No grupo de segurança de rede criado na etapa anterior, **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Protocolo **(*)** .
* Direção de **Entrada**.
* Origem **(*)** .
* Destino **(*)** .
* Porta de destino **Porta 80**.
* Acesso **Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria:

* Adaptadores de rede para os servidores back-end.
* Um arquivo de configuração de nuvem chamado **cloud-init.txt** para a configuração do servidor.
* Duas máquinas virtuais a serem usadas como servidores back-end para o balanceador de carga.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Crie dois adaptadores de rede com [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Chamada **myNicVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Chamada **myNicVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

### <a name="create-cloud-init-configuration-file"></a>Criar arquivo de configuração cloud-init

Use um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js "Olá, Mundo" em uma máquina virtual Linux. 

No shell atual, crie um arquivo chamado cloud-init.txt. Copie e cole a configuração a seguir no shell. Certifique-se de copiar todo o arquivo cloud-init corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```
### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Chamada **myVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM1**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* Na **Zona 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Chamada **myVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM2**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* Na **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

Pode levar alguns minutos para que as VMs sejam implantadas.

## <a name="create-standard-load-balancer"></a>Criar Standard Load Balancer

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Chamado **myLoadBalancer**.
* Um pool de front-end chamado **myFrontEnd**.
* Um pool de back-end chamado **myBackEndPool**.
* Associado à rede virtual **myVNet**.
* Associado à sub-rede de back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP de front-end para o tráfego de entrada.
* O pool de IPs de back-end para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.
* Tempo limite de ociosidade de **15 minutos**.
* Habilitar redefinição de TCP/IP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>As máquinas virtuais do pool de back-end não terão conectividade de saída com a Internet com essa configuração. </br> Para obter mais informações sobre como fornecer a conectividade de saída, confira: </br> **[Conexões de saída no Azure](load-balancer-outbound-connections.md)**</br> Opções para fornecer conectividade: </br> **[Configuração do balanceador de carga somente de saída](egress-only.md)** </br> **[O que é NAT de Rede Virtual?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicionar máquinas virtuais ao pool de back-end do balanceador de carga

Adicione as máquinas virtuais ao pool de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao adaptador de rede **myNicVM1** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao adaptador de rede **myNicVM2** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>O balanceador de carga de SKU Standard é recomendado para cargas de trabalho de produção. Para obter mais informações sobre SKUs, confira **[SKUs do Azure Load Balancer](skus.md)** .

## <a name="configure-virtual-network"></a>Configurar rede virtual

Antes de implantar as VMs e o balanceador de carga, crie os recursos da rede virtual de suporte.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando [az network vnet create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* Chamada **myVNet**.
* Prefixo de endereço **10.1.0.0/16**.
* Sub-rede chamada **myBackendSubnet**.
* Prefixo de sub-rede **10.1.0.0/24**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Localização de **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Para um Standard Load Balancer, as VMs no endereço de back-end precisam ter adaptadores de rede que pertençam a um grupo de segurança de rede. 

Crie um grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Chamado **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Criar uma regra de grupo de segurança de rede

Crie uma regra de grupo de segurança de rede usando [az network nsg create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Chamada **myNSGRuleHTTP**.
* No grupo de segurança de rede criado na etapa anterior, **myNSG**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Protocolo **(*)** .
* Direção de **Entrada**.
* Origem **(*)** .
* Destino **(*)** .
* Porta de destino **Porta 80**.
* Acesso **Permitir**.
* Prioridade **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Criar interfaces de rede para as máquinas virtuais

Crie dois adaptadores de rede com [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Chamada **myNicVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Chamada **myNicVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria:

* Um arquivo de configuração de nuvem chamado **cloud-init.txt** para a configuração do servidor. 
* Conjunto de disponibilidade para as máquinas virtuais
* Duas máquinas virtuais a serem usadas como servidores back-end para o balanceador de carga.

Para verificar se o balanceador de carga foi criado com êxito, instale o NGINX nas máquinas virtuais.

### <a name="create-cloud-init-configuration-file"></a>Criar arquivo de configuração cloud-init

Use um arquivo de configuração cloud-init para instalar o NGINX e executar um aplicativo Node.js "Olá, Mundo" em uma máquina virtual Linux. 

No shell atual, crie um arquivo chamado cloud-init.txt. Copie e cole a configuração a seguir no shell. Certifique-se de copiar todo o arquivo cloud-init corretamente, especialmente a primeira linha:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-availability-set-for-virtual-machines"></a>Criar conjunto de disponibilidade para máquinas virtuais

Crie a conjunto de disponibilidade com [az vm availability-set create](/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* Chamado **myAvSet**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Localização **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie as máquinas virtuais com [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Chamada **myVM1**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM1**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* No conjunto de disponibilidade **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Chamada **myVM2**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicVM2**.
* Imagem de máquina virtual **UbuntuLTS**.
* O arquivo de configuração **cloud-init.txt** criado na etapa anterior.
* Na **Zona 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

Pode levar alguns minutos para que as VMs sejam implantadas.

## <a name="create-basic-load-balancer"></a>Criar o balanceador de carga básico

Esta seção fornece detalhes sobre como criar e configurar os componentes do balanceador de carga abaixo:

  * Um pool de IPs de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
  * Um pool de IPs de back-end ao qual o pool de front-end envia o tráfego de rede com a carga balanceada.
  * Uma investigação de integridade que determina a integridade das instâncias de VM de back-end.
  * Uma regra de balanceador de carga que define como o tráfego é distribuído para as VMs.

### <a name="create-the-load-balancer-resource"></a>Criar o recurso do balanceador de carga

Crie um balanceador de carga público com [az network lb create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Chamado **myLoadBalancer**.
* Um pool de front-end chamado **myFrontEnd**.
* Um pool de back-end chamado **myBackEndPool**.
* Associado à rede virtual **myVNet**.
* Associado à sub-rede de back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Criar a investigação de integridade

Uma investigação de integridade verifica todas as instâncias da máquina virtual para garantir que elas possam enviar tráfego de rede. 

Uma máquina virtual com uma verificação de investigação com falha é removida do balanceador de carga. A máquina virtual será adicionada novamente ao balanceador de carga quando a falha for resolvida.

Crie uma investigação de integridade com [az network lb probe create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Monitora a integridade das máquinas virtuais.
* Chamado **myHealthProbe**.
* Protocolo **TCP**.
* Monitorando a **Porta 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Criar a regra de balanceador de carga

Uma regra de balanceador de carga define:

* A configuração do IP de front-end para o tráfego de entrada.
* O pool de IPs de back-end para receber o tráfego.
* As portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Chamada **myHTTPRule**
* Escutando na **Porta 80** no pool de front-end **myFrontEnd**.
* Enviando tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool** usando a **Porta 80**. 
* Usando a investigação de integridade **myHealthProbe**.
* Protocolo **TCP**.
* Tempo limite de ociosidade de **15 minutos**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Adicionar máquinas virtuais ao pool de back-end do balanceador de carga

Adicione as máquinas virtuais ao pool de back-end com [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao adaptador de rede **myNicVM1** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* No pool de endereços de back-end **myBackEndPool**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Associada ao adaptador de rede **myNicVM2** e a **ipconfig1**.
* Associada ao balanceador de carga **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga

### <a name="create-azure-bastion-public-ip"></a>Criar um IP público do Azure Bastion

Use [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) para criar um endereço IP público para o bastion host:

* Crie um endereço IP público com redundância de zona padrão chamado **myBastionIP**.
* Em **CreateIntLBQS-rg**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Criar uma sub-rede do Azure Bastion

Use [az network vnet subnet create](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) para criar uma sub-rede:

* Chamada **AzureBastionSubnet**.
* Prefixo de endereço **10.1.1.0/24**.
* Na rede virtual **myVNet**.
* No grupo de recursos **CreateIntLBQS-rg**.

```azurecli-interactive
  az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Criar um host do Azure Bastion
Use [az network bastion create](/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) para criar um bastion host:

* Chamado **myBastionHost**
* Em **CreateIntLBQS-rg**
* Associado ao IP público **myBastionIP**.
* Associado à rede virtual **myVNet**.
* Na localização **eastus**.

```azurecli-interactive
  az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
Levará alguns minutos para que o bastion host seja implantado.

### <a name="create-test-virtual-machine"></a>Criar máquina virtual de teste

Crie os adaptadores de rede com [az network nic create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

* Chamado **myNicTestVM**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Na rede virtual **myVNet**.
* Na sub-rede **myBackendSubnet**.
* No grupo de segurança de rede **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Crie a máquina virtual com [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

* Chamada **myTestVM**.
* No grupo de recursos **CreateIntLBQS-rg**.
* Anexada ao adaptador de rede **myNicTestVM**.
* Imagem de máquina virtual **Win2019Datacenter**.
* Escolha valores para **\<adminpass>** e **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
Pode levar alguns minutos para que a máquina virtual seja implantada.

### <a name="test"></a>Teste

1. [Entre](https://portal.azure.com) no portal do Azure.

1. Encontre o endereço IP privado para o balanceador de carga na tela **Visão Geral**. Clique em **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e depois selecione **myLoadBalancer**.

2. Anote ou copie o endereço ao lado de **Endereço IP Privado** na **Visão geral** do **myLoadBalancer**.

3. Selecione **Todos os serviços** no menu à esquerda, selecione **Todos os recursos** e, na lista de recursos, escolha **myTestVM**, que está no grupo de recursos **CreateIntLBQS-rg**.

4. Na página **Visão Geral**, selecione **Conectar** e **Bastion**.

6. Insira o nome de usuário e a senha fornecidos durante a criação da VM.

7. Abra o **Internet Explorer** em **myTestVM**.

8. Insira o endereço IP da etapa anterior na barra de endereços do navegador. A página padrão do servidor Web do IIS é exibida no navegador.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Criar um balanceador de carga interno Standard" border="true":::
   
Para ver o balanceador de carga distribuir o tráfego entre as três VMs, personalize a página padrão do servidor Web do IIS de cada VM e force a atualização do navegador da Web no computador cliente.

## <a name="clean-up-resources"></a>Limpar os recursos

Use o comando [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) para remover o grupo de recursos, o balanceador de carga e todos os recursos relacionados quando deixarem de ser necessários.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Próximas etapas
Neste início rápido

* Você criou um balanceador de carga público padrão ou público
* Anexou máquinas virtuais. 
* Configurou a regra de tráfego e a investigação de integridade do balanceador de carga.
* Testou o balanceador de carga.

Para saber mais sobre o Azure Load Balancer, vá para 
> [!div class="nextstepaction"]
> [O que é o Azure Load Balancer?](load-balancer-overview.md)