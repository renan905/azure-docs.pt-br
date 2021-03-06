---
title: Exemplo do PowerShell – atribuir grupo a um aplicativo do Proxy de Aplicativo
description: Exemplo do PowerShell que atribui um grupo a um aplicativo do Proxy de Aplicativo do Azure AD (Azure Active Directory).
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 26e0506cbea5cb34efaf516c3f4c01d830a219f0
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861690"
---
# <a name="assign-a-group-to-a-specific-azure-ad-application-proxy-application"></a>Atribuir um grupo a um aplicativo específico do Proxy de Aplicativo do Azure AD

Este exemplo de script do PowerShell permite atribuir um grupo específico a um aplicativo do Proxy de Aplicativo do Azure AD (Azure Active Directory).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Este exemplo requer o [módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2) (AzureAD) ou a [versão prévia do módulo do PowerShell do AzureAD v2 para o Graph](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview).

## <a name="sample-script"></a>Exemplo de script

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-group-to-app.ps1 "Assign a group to a specific Azure AD Application Proxy application")]

## <a name="script-explanation"></a>Explicação sobre o script

| Comando | Observações |
|---|---|
| [New-AzureADGroupAppRoleAssignment](/powershell/module/AzureAD/New-azureadgroupapproleassignment) | Atribui um grupo a uma função de aplicativo. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do PowerShell do Azure AD, confira [Visão geral do módulo do PowerShell do Azure AD](/powershell/azure/active-directory/overview).

Para obter outros exemplos do PowerShell para o Proxy de Aplicativo, confira [Exemplos do PowerShell do Azure AD para o Proxy de Aplicativo do Azure AD](../application-proxy-powershell-samples.md).
