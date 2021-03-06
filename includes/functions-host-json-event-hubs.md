---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: f5101233f7995fb58fc530e613ba3235a55c783c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628648"
---
### <a name="functions-2x-and-higher"></a>Funções 2.x e posteriores

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTime": ""
            }
        }
    }
}  
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------|
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação do cursor do EventHub.|
|eventProcessorOptions/maxBatchSize|10|A contagem máxima de eventos recebidos por loop de recebimento.|
|eventProcessorOptions/prefetchCount|300|A contagem da pré-busca padrão usada pelo `EventProcessorHost` subjacente. O valor mínimo permitido é 10.|
|initialOffsetOptions/type|fromStart|O local no fluxo de eventos do qual iniciar o processamento quando um ponto de verificação não existir no armazenamento. As opções são `fromStart`, `fromEnd` ou `fromEnqueuedTime`. O `fromEnd` processa novos eventos que foram enfileirados depois que o aplicativo de funções começou a ser executado. Aplica-se a todas as partições.  Para obter mais informações, consulte a [documentação do EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet).|
|initialOffsetOptions/enqueuedTime|N/D| Especifica o tempo de enfileiramento do evento no fluxo do qual iniciar o processamento. Quando `initialOffsetOptions/type` é configurado como `fromEnqueuedTime`, essa configuração é obrigatória. Compatível com hora em qualquer formato com suporte do [DateTime.Parse()](/dotnet/standard/base-types/parsing-datetime), como `2020-10-26T20:31Z`. Para maior clareza, você também deve especificar um fuso horário. Quando o fuso horário não é especificado, o Functions assume o fuso horário local do computador que está executando o aplicativo de funções, que é o UTC quando executado no Azure. Para obter mais informações, consulte a [documentação do EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet).|
> [!NOTE]
> Para obter uma referência de host.json no Azure Functions 2.x e posterior, confira [Referência de host.json para o Azure Functions](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Funções 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Propriedade  |Padrão | Descrição |
|---------|---------|---------| 
|maxBatchSize|64|A contagem máxima de eventos recebidos por loop de recebimento.|
|prefetchCount|n/a|A pré-busca padrão que será usada pelo `EventProcessorHost` subjacente.| 
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação do cursor do EventHub.| 

> [!NOTE]
> Para obter uma referência de host.json no Azure Functions 1.x, confira [Referência de host.json para o Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md).
