---
title: Suporte a contêiner
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de instância de contêiner do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009833"
---
## <a name="create-an-azure-container-instance-resource"></a>Criar um recurso de instância de contêiner do Azure

1. Vá para a página [criar](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) para instâncias de contêiner.

2. Na guia **noções básicas** , insira os seguintes detalhes:

    |Configuração|Valor|
    |--|--|
    |Subscription|Selecione sua assinatura.|
    |Resource group|Selecione o grupo de recursos disponível ou crie um novo, como `cognitive-services` .|
    |Nome do contêiner|Insira um nome como `cognitive-container-instance` . O nome deve estar em minúsculas.|
    |Localização|Selecione uma região para implantação.|
    |Tipo de Imagem|Se a imagem de contêiner estiver armazenada em um registro de contêiner que não requer credenciais, escolha `Public` . Se o acesso à sua imagem de contêiner exigir credenciais, escolha `Private` . Consulte [repositórios e imagens de contêiner](../../cognitive-services-container-support.md#container-repositories-and-images) para obter detalhes sobre se a imagem de contêiner é ou não `Public` ou `Private` ("visualização pública"). |
    |Nome da imagem|Insira o local do contêiner de serviços cognitivas. O local é o que é usado como um argumento para o `docker pull` comando. Consulte os [repositórios e as imagens do contêiner](../../cognitive-services-container-support.md#container-repositories-and-images) para obter os nomes de imagem disponíveis e seu repositório correspondente.<br><br>O nome da imagem deve ser totalmente qualificado, especificando três partes. Primeiro, o registro de contêiner, em seguida, o repositório, por fim, o nome da imagem: `<container-registry>/<repository>/<image-name>` .<br><br>Aqui está um exemplo, `mcr.microsoft.com/azure-cognitive-services/keyphrase` que representaria a imagem de extração de frases-chave no registro de contêiner da Microsoft no repositório de serviços cognitivas do Azure. Outro exemplo é, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` que representaria a imagem de fala para texto no repositório da Microsoft do registro de contêiner de visualização do contêiner. |
    |Tipo do SO|`Linux`|
    |Tamanho|Altere o tamanho para as recomendações sugeridas para seu contêiner de serviço cognitiva específico:<br>2 núcleos de CPU<br>4 GB

3. Na guia **rede** , insira os seguintes detalhes:

    |Configuração|Valor|
    |--|--|
    |Portas|Defina a porta TCP como `5000` . Expõe o contêiner na porta 5000.|

4. Na guia **avançado** , insira as variáveis de **ambiente** necessárias para as configurações de cobrança de contêiner do recurso de instância de contêiner do Azure:

    | Chave | Valor |
    |--|--|
    |`apikey`|Copiado da página de **chaves** do recurso. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
    |`billing`|Copiado da página de **visão geral** do recurso.|
    |`eula`|`accept`|

5. Clique em **revisar e criar**
6. Após a aprovação da validação, clique em **criar** para concluir o processo de criação
7. Quando o recurso for implantado com êxito, ele estará pronto
