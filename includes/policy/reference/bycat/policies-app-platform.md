---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6fb0c3dc7095c1fc48ba3ed26b3a2f7ab47ac789
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "96007455"
---
|Nome<br /><sub>(Portal do Azure)</sub> |Descrição |Efeito(s) |Versão<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Auditar instâncias do Azure Spring Cloud nas quais o rastreamento distribuído não está habilitado](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |As ferramentas de rastreamento distribuído do Azure Spring Cloud permitem a depuração e o monitoramento das interconexões complexas entre os microsserviços de um aplicativo. As ferramentas de rastreamento distribuído precisam estar habilitadas e em um estado íntegro. |Audit, desabilitado |[1.0.0 – versão prévia](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[O Azure Spring Cloud deve usar injeção de rede](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |As instâncias do Azure Spring Cloud devem usar injeção de rede virtual para as seguintes finalidades: 1. Isole o Azure Spring Cloud da Internet. 2. Habilite a interação do Azure Spring Cloud com sistemas em data centers locais ou no serviço do Azure em outras redes virtuais. 3. Capacite os clientes a controlar comunicações de rede de entrada e saída para o Azure Spring Cloud. |Auditoria, desabilitado, negação |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
