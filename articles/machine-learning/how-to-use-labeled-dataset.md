---
title: Criar e explorar conjuntos de dados com rótulos
titleSuffix: Azure Machine Learning
description: Saiba como exportar rótulos de dados de seus projetos de rotulagem do Azure Machine Learning e usá-los para tarefas de machine learning.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: b7eaf374e72997013f09b1c94ce16c67d115a818
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987364"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Criar e explorar conjunto de dados do Azure Machine Learning com rótulos

Neste artigo, você aprenderá a exportar os rótulos de dados de um projeto de rotulagem de dados do Azure Machine Learning e carregá-los em formatos populares, como um dataframe do Pandas para exploração de dados ou um conjunto de dados Torchvision para transformação de imagem. 

## <a name="what-are-datasets-with-labels"></a>O que são conjuntos de dados com rótulos 

Nós nos referimos a Azure Machine Learning conjuntos de valores com rótulos como conjuntos de valores rotulados. Esses tipos específicos de conjuntos de dados rotulados são criados somente como uma saída de Azure Machine Learning projetos de rótulos de dados. Crie um projeto de rotulagem de dados com [estas etapas](how-to-create-labeling-projects.md). O Machine Learning dá suporte a projetos de rotulagem de dados para classificação de imagem (de vários rótulos ou multiclasse) e à identificação do objeto junto com caixas delimitadoras.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) ou acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).
    * Instalar o pacote [azure-contrib-dataset](/python/api/azureml-contrib-dataset/?preserve-view=true&view=azure-ml-py)
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).
* Acesso a um projeto de rotulagem de dados do Azure Machine Learning. Se você não tiver um projeto de rotulagem, crie um com [estas etapas](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Exportar rótulos de dados 

Ao concluir um projeto de rotulagem de dados, você pode exportar os dados do rótulo de um projeto de rotulagem. Fazer isso permite que você capture a referência aos dados e seus rótulos e exporte-os no [formato COCO](http://cocodataset.org/#format-data) ou como um conjunto de dados do Azure Machine Learning. Use o botão **Exportar** na página **Detalhes do projeto** do projeto de rotulagem.

### <a name="coco"></a>COCO 

 O arquivo COCO é criado no armazenamento de blobs padrão do Workspace do Azure Machine Learning em uma pasta dentro de *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Conjunto de dados do Azure Machine Learning

Acesse o conjunto de dados exportado do Azure Machine Learning na seção **Conjuntos de dados** do Azure Machine Learning Studio. A página de **Detalhes** do conjunto de dados também fornece um código de exemplo para acessar seus rótulos por meio do Python.

![Conjunto de dados exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Explorar conjuntos de valores rotulados

Carregue seus conjuntos de dados rotulados em um dataframe do Pandas ou conjunto de dados Torchvision para aproveitar bibliotecas populares de software livre para exploração de dados, bem como bibliotecas fornecidas pelo PyTorch para treinamento e transformação de imagens.

### <a name="pandas-dataframe"></a>Dataframe do Pandas

Você pode carregar conjuntos de dados rotulados para um dataframe do Pandas com o método [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) da classe `azureml-contrib-dataset`. Instale a classe com o seguinte comando de shell: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>O namespace azureml.contrib muda com frequência, enquanto trabalhamos para aprimorar o serviço. Assim, tudo nesse namespace deve ser considerado como uma versão prévia e sem o suporte total da Microsoft.

O Azure Machine Learning oferece as seguintes opções de manipulação de arquivo para fluxos de arquivo ao converter para um dataframe do pandas.
* Baixar: Baixe seus arquivos de dados para um caminho local.
* Montagem: monte seus arquivos de dados em um ponto de montagem. A montagem só funciona para computação baseada em Linux, incluindo VM do notebook do Azure Machine Learning e Computação do Azure Machine Learning.

No código a seguir, o `animal_labels` conjunto de resultados é a saída de um projeto de rotulagem salvo anteriormente no espaço de trabalho.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Conjuntos de dados Torchvision

Você pode carregar conjuntos de dados rotulados para o conjunto de dados Torchvision com o método [to_torchvision()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-torchvision--) também da classe `azureml-contrib-dataset`. Para usar esse método, você precisa ter o [PyTorch](https://pytorch.org/) instalado. 

No código a seguir, o `animal_labels` conjunto de resultados é a saída de um projeto de rotulagem salvo anteriormente no espaço de trabalho.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Próximas etapas

* Confira o [conjunto de dados com o notebook de rótulos](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) para obter um exemplo de treinamento completo.
