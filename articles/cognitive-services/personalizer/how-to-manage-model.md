---
title: Gerenciar configurações de modelo e aprendizado-personalizador
description: As configurações de modelo e aprendizado aprendidas por computador podem ser exportadas para backup em seu próprio sistema de controle do código-fonte.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: f82adad5273f1c5559cbeb1924f59e57c863f6b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303447"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Como gerenciar configurações de modelo e aprendizado

As configurações de modelo e aprendizado aprendidas por computador podem ser exportadas para backup em seu próprio sistema de controle do código-fonte.

## <a name="export-the-personalizer-model"></a>Exportar o modelo do Personalizador

Na seção do gerenciamento de recursos para **configurações de modelo e aprendizado**, reveja criação de modelo e data da última atualização e exporte o modelo atual. É possível usar o portal do Azure ou as APIs do Personalizador para exportar um arquivo de modelo para fins de arquivamento.

![Exportar o modelo atual do Personalizador](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Limpar dados para seu loop de aprendizado

1. Na portal do Azure, para o recurso personalizador, na página **configurações de modelo e aprendizado** , selecione **limpar dados**.
1. Para limpar todos os dados e redefinir o loop de aprendizagem para o estado original, marque todas as 3 caixas de seleção.

    ![Em portal do Azure, desmarque dados do recurso Personalizar.](./media/settings/clear-data-from-personalizer-resource.png)

    |Valor|Finalidade|
    |--|--|
    |Personalização registrada e dados de recompensa.|Esses dados de log são usados em avaliações offline. Limpe os dados se você estiver redefinindo seu recurso.|
    |Redefina o modelo personalizador.|Esse modelo é alterado em cada novo treinamento. Essa frequência de treinamento é especificada na **frequência do modelo de carregamento** na página de **configuração** . |
    |Defina a política de aprendizado como padrão.|Se você alterou a política de aprendizado como parte de uma avaliação offline, isso redefinirá a política de aprendizado original.|

1. Selecione **limpar dados selecionados** para iniciar o processo de limpeza. O status é relatado nas notificações do Azure, na navegação superior direita.

## <a name="import-a-new-learning-policy"></a>Importar uma nova política de aprendizado

As configurações da [política de aprendizado](concept-active-learning.md#understand-learning-policy-settings) determinam os _hiperparâmetros_ do treinamento do modelo. Execute uma [avaliação offline](how-to-offline-evaluation.md) para encontrar uma nova política de aprendizado.

1. Abra o [portal do Azure](https://portal.azure.com)e selecione o recurso personalizador.
1. Selecione **configurações de modelo e aprendizado** na seção **Gerenciamento de recursos** .
1. Para **Importar configurações de aprendizado** , selecione o arquivo que você criou com o formato JSON especificado acima e, em seguida, selecione o botão **carregar** .

    Aguarde a notificação de que a política de aprendizado foi carregada com êxito.

## <a name="export-a-learning-policy"></a>Exportar uma política de aprendizado

1. Abra o [portal do Azure](https://portal.azure.com)e selecione o recurso personalizador.
1. Selecione **configurações de modelo e aprendizado** na seção **Gerenciamento de recursos** .
1. Para **Importar configurações de aprendizado** , selecione o botão **exportar configurações de aprendizado** . Isso salva o `json` arquivo em seu computador local.

## <a name="next-steps"></a>Próximas etapas

[Saiba como gerenciar uma política de aprendizado](how-to-manage-model.md)
