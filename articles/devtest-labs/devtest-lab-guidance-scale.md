---
title: Expanda sua infraestrutura do Azure DevTest Labs
description: Este artigo fornece diretrizes para expandir a infraestrutura do Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 50bf08678a12a1a0499abd08c52a264d03f4a401
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478783"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Expanda sua infraestrutura do Azure DevTest Labs
Antes de implementar o DevTest Labs em escala empresarial, há vários pontos de decisão essenciais. Conhecer a fundo esses pontos de decisão ajuda uma organização a tomar decisões de design no futuro. No entanto, esses pontos não devem impedir uma organização de iniciar uma prova de conceito. As três principais áreas para o planejamento inicial de expansão são:

- Rede e segurança
- Topologia de assinatura
- Funções e responsabilidades

## <a name="networking-and-security"></a>Rede e segurança
Rede e segurança são os alicerces de todas as organizações. Enquanto uma implantação em toda a empresa requer uma análise muito mais aprofundada, há um número reduzido de requisitos para executar uma prova de conceito com êxito. Algumas áreas de foco principais incluem:

- **Assinatura do Azure** – para implantar o DevTest Labs, você precisa ter acesso a uma assinatura do Azure com direitos apropriados para criar recursos. Há várias maneiras de obter acesso às assinaturas do Azure, incluindo um Contrato Enterprise e pago conforme o uso. Para obter mais informações sobre como obter acesso a uma assinatura do Azure, confira [Licenciamento do Azure para a empresa](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Acesso a recursos locais** – algumas organizações exigem que seus recursos no DevTest Labs tenham acesso a recursos locais. É necessária uma conexão segura do seu ambiente local para o Azure. Portanto, é importante que você defina/configure uma conexão VPN ou do ExpressRoute antes de começar. Para obter mais informações, confira [Visão geral de redes virtuais](../virtual-network/virtual-networks-overview.md).
- **Requisitos de segurança adicionais** – outros requisitos de segurança, tais como políticas de computador, acesso a endereços IP públicos e conexão com a Internet, são cenários que precisam ser revisados antes da implementação de uma prova de conceito. 

## <a name="subscription-topology"></a>Topologia de assinatura
A topologia de assinatura é uma consideração de design essencial ao implantar o DevTest Labs para a empresa. No entanto, antes da conclusão de uma prova de conceito, essa topologia não é necessária para consolidar todas as decisões. Ao avaliar o número de assinaturas necessárias para uma implementação empresarial, há dois extremos: 

- Uma assinatura para toda a organização
- Assinatura por usuário

Em seguida, podemos destacar os pontos positivos de cada abordagem.

### <a name="one-subscription"></a>Uma assinatura
Geralmente, a abordagem de uma assinatura não é gerenciável em uma grande empresa. No entanto, limitar o número de assinaturas fornece os seguintes benefícios:

- **Previsão** dos custos para a empresa.  Planejar o orçamento torna-se muito mais fácil em uma assinatura única, porque todos os recursos estão em um único pool. Essa abordagem permite tomada de decisões mais simples quanto a quando exercer medidas de controle de custos em qualquer determinado momento em um ciclo de cobrança.
- A **capacidade de gerenciamento** de VMs, artefatos, fórmulas, configuração de rede, permissões, políticas etc. é mais fácil, já que todas as atualizações só são necessárias em uma assinatura em vez de fazer atualizações em várias assinaturas.
- O esforço referente à **rede** é bastante simplificado em uma assinatura única para empresas em que a conectividade local é um requisito. Conectar redes virtuais entre assinaturas (modelo hub-spoke) é necessário com assinaturas adicionais, o que requer configuração, espaços de endereço IP e gerenciamento adicionais, etc.
- A **colaboração em equipe** é mais fácil quando todos estão trabalhando na mesma assinatura – por exemplo, é mais fácil reatribuir uma VM para um colega de trabalho, compartilhar recursos de equipe, etc.

### <a name="subscription-per-user"></a>Assinatura por usuário
Uma assinatura separada por usuário fornece oportunidades iguais no espectro de alternativas. Os benefícios de ter várias assinaturas incluem:

- As **cotas de dimensionamento do Azure** não impedirão a adoção. Por exemplo, no momento da redação deste artigo, o Azure permite 200 contas de armazenamento por assinatura. Existem cotas operacionais para a maioria dos serviços do Azure (muitas podem ser personalizadas, outras não). Nesse modelo de uma assinatura por usuário, é muito improvável que a maioria das cotas sejam atingidas. Para obter mais informações sobre as cotas de dimensionamento atuais do Azure, confira [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md).
- **Estornos** a desenvolvedores individuais ou a grupos de desenvolvedores tornam-se muito mais fáceis, permitindo que as organizações contabilizem os custos usando seu modelo atual.
- Os conceitos de **propriedade e permissões** dos ambientes do DevTest Labs são simples. Você permite aos desenvolvedores acesso em nível de assinatura e eles são 100% responsáveis por tudo, incluindo a configuração de rede, políticas de laboratório e gerenciamento de VMs.

Na empresa, pode já haver restrições suficientes nos extremos do espectro. Portanto, talvez você precise configurar assinaturas de uma maneira intermediária a esses extremos. Como prática recomendada, a meta de uma organização deve ser usar o menor número de assinaturas possível, tendo em mente as funções de imposição que aumentam o número total de assinaturas. Reiterando, a topologia de assinatura é essencial para uma implantação empresarial do DevTest Labs, mas não deve ser motivo para atrasar uma prova de conceito. Há detalhes adicionais no artigo [Governança](devtest-lab-guidance-governance-policy-compliance.md) sobre como decidir sobre granularidade de laboratórios e de assinaturas na organização.

## <a name="roles-and-responsibilities"></a>Funções e responsabilidades
Uma prova de conceito do DevTest Labs tem três funções principais com responsabilidades definidas – proprietário da assinatura, proprietário do DevTest Labs, usuário do DevTest Labs e, opcionalmente, um colaborador.

- **Proprietário da assinatura** – o proprietário da assinatura tem direitos para administrar uma assinatura do Azure, incluindo a atribuição de usuários, o gerenciamento de políticas, a criação de & o gerenciamento de topologia de rede, a solicitação de aumento de cota, etc. Para obter mais informações, consulte [Este artigo](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **Proprietário do DevTest Labs** – o proprietário do DevTest Labs tem acesso administrativo total ao laboratório. Essa pessoa é responsável por adicionar/remover usuários, gerenciar configurações de custo, configurações gerais de laboratório e outras tarefas baseadas em VM/artefato. Um proprietário de laboratório também tem todos os direitos de um usuário do DevTest Labs.
- **Usuário do DevTest Labs** – pode criar e consumir as máquinas virtuais no laboratório. Esses indivíduos têm algumas capacidades administrativas mínimas nas VMs que eles criam (iniciar/parar/excluir/configurar as respectivas VMs). Os usuários não podem gerenciar VMs de outros usuários.

## <a name="next-steps"></a>Próximas etapas
Veja o próximo artigo desta série: [Orquestrar a implementação do Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)