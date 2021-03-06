---
title: Criar um IP público-Azure PowerShell
description: Saiba como criar um IP público usando Azure PowerShell
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 99e79e4d094fe6e93510d139d2f4d08f260102df
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010037"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-powershell"></a>Início rápido: criar um endereço IP público usando Azure PowerShell

Este artigo mostra como criar um recurso de endereço IP público usando Azure PowerShell. Para obter mais informações sobre a quais recursos isso pode ser associado, a diferença entre o SKU básico e Standard e outras informações relacionadas, consulte [endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  Para este exemplo, nos concentraremos somente nos endereços IPv4; para obter mais informações sobre endereços IPv6, consulte [IPv6 para VNet do Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Pré-requisitos

- Azure PowerShell instalado localmente ou Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) chamado **MyResource** Group no local **eastus2** .

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'

New-AzResourceGroup -Name $rg -Location $loc
```
## <a name="create-public-ip"></a>Criar IP público

---
# <a name="standard-sku---using-zones"></a>[**SKU padrão-usando zonas**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>O comando a seguir funciona para a API versão 2020-08-01 ou posterior.  Para obter mais informações sobre a versão de API que está sendo usada no momento, consulte [provedores de recursos e tipos](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público com redundância de zona padrão chamado **MyStandardZRPublicIP** no **myresourceproperties**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZRPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 1,2,3

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```
> [!IMPORTANT]
> Para versões da API com mais de 2020-08-01, execute o comando acima sem especificar um parâmetro de zona para criar um endereço IP com redundância de zona. 
>

Para criar um endereço IP público de zona padrão no Zona 2 chamado **myStandardZonalPublicIP** no **MyResource**, use o seguinte comando:

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardZonalPublicIP'
$sku = 'Standard'
$alloc = 'Static'
$zone = 2

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku -zone $zone
```

Observe que as opções acima para zonas são apenas seleções válidas em regiões com [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**SKU padrão-sem zonas**](#tab/option-create-public-ip-standard)

>[!NOTE]
>O comando a seguir funciona para a API versão 2020-08-01 ou posterior.  Para obter mais informações sobre a versão de API que está sendo usada no momento, consulte [provedores de recursos e tipos](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público padrão como um recurso não zonal chamado **MyStandardPublicIP** no **myresourceproperties**.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myStandardPublicIP'
$sku = 'Standard'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```

Essa seleção é válida em todas as regiões e é a seleção padrão para endereços IP públicos padrão em regiões sem [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**SKU Básico**](#tab/option-create-public-ip-basic)

Use [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) para criar um endereço IP público estático básico chamado **MyBasicPublicIP** no **MyResource**.  Os IPs públicos básicos não têm o conceito de zonas de disponibilidade.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$loc = 'eastus2'
$pubIP = 'myBasicPublicIP'
$sku = 'Basic'
$alloc = 'Static'

New-AzPublicIpAddress -ResourceGroupName $rg -Name $pubIP -Location $loc -AllocationMethod $alloc -SKU $sku
```
Se for aceitável que o endereço IP seja alterado ao longo do tempo, a atribuição de IP **dinâmico** poderá ser selecionada alterando o AllocationMethod para ' dinâmico '.

---

## <a name="additional-information"></a>Informações adicionais 

Para obter mais detalhes sobre as variáveis individuais listadas acima, consulte [gerenciar endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Próximas etapas
- Associar um [endereço IP público a uma máquina virtual](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Saiba mais sobre os [endereços IP públicos](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) no Azure.
- Saiba mais sobre todas as [configurações de endereço IP público](virtual-network-public-ip-address.md#create-a-public-ip-address).
