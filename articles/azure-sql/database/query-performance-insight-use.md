---
title: Análise de Desempenho de Consultas
description: O monitoramento de desempenho de consultas identifica as consultas de longa duração e consumo de CPU para bancos de dados individuais e em pool no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: a016781e5b05c8f43c043bf1f3368a58064e43ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501253"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Análise de Desempenho de Consultas para Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Análise de Desempenho de Consultas fornece a análise de consulta inteligente para bancos de dados individuais e em pool. Ela ajuda a identificar as principais consultas de consumo de recursos e de execução prolongada na carga de trabalho. Isso ajuda a localizar as consultas a serem otimizadas para melhorar o desempenho geral da carga de trabalho e usar com eficiência o recurso que você está pagando. Análise de Desempenho de Consultas ajuda a gastar menos tempo Solucionando problemas de desempenho do banco de dados, fornecendo:

* Informações mais aprofundadas sobre o consumo de seu recurso de bancos de dados (DTU)
* Detalhes sobre as principais consultas de banco de dados por CPU, duração e contagem de execução (possíveis candidatos de ajuste para melhorias de desempenho)
* A capacidade de fazer uma busca detalhada em detalhes de uma consulta para exibir o texto da consulta e o histórico da utilização de recursos
* Anotações que mostram recomendações de desempenho de [consultores de banco de dados](database-advisor-implement-performance-recommendations.md)

![Análise de Desempenho de Consultas](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Pré-requisitos

A Análise de Desempenho de Consultas exige a execução do [Repositório de Consultas](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) em seu banco de dados. Por padrão, ele é habilitado automaticamente para todos os bancos de dados no Azure SQL Database. Se o Repositório de Consultas não estiver em execução, o portal do Azure solicitará que você habilite-o.

> [!NOTE]
> Se a mensagem "O Repositório de Consultas não está configurado corretamente neste banco de dados" for exibida no portal, consulte [Otimizar a configuração do Repositório de Consultas](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Permissões

Você precisa das seguintes permissões do Azure [RBAC (controle de acesso baseado em função)](../../role-based-access-control/overview.md) para usar o análise de desempenho de consultas:

* As permissões **Leitor**, **Proprietário**, **Colaborador**, **Colaborador do DB SQL** ou **Colaborador do SQL Server** são necessárias para exibir as principais consultas e gráficos que consomem muitos recursos.
* As permissões **Proprietário**, **Colaborador**, **Colaborador do Banco de Dados SQL** ou **Colaborador do SQL Server** são necessárias para exibir o texto da consulta.

## <a name="use-query-performance-insight"></a>Usar Análise de Desempenho de Consultas

A Visão do Desempenho de Consulta é fácil de usar:

1. Abra o [portal do Azure](https://portal.azure.com/) e localize um banco de dados que você quer examinar.
2. No menu do lado esquerdo, abra análise de desempenho de consultas de **desempenho inteligente**  >  **Query Performance Insight**.
  
   ![Análise de Desempenho de Consultas no menu](./media/query-performance-insight-use/tile.png)

3. Na primeira guia, examine a lista das consultas que consomem mais recursos.
4. Escolha uma consulta individual para exibir seus detalhes.
5. Abra **Intelligent Performance**  >  **recomendações de desempenho** de desempenho inteligente e verifique se há recomendações de desempenho disponíveis. Para obter mais informações sobre recomendações de desempenho internas, consulte [Assistente do banco de dados SQL do Azure](database-advisor-implement-performance-recommendations.md).
6. Use controles deslizantes ou ícones Ampliar para alterar o intervalo observado.

   ![Painel de desempenho](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Para que o banco de dados SQL do Azure processe as informações em Análise de Desempenho de Consultas, Repositório de Consultas precisa capturar algumas horas. Se o banco de dados não tiver atividades ou se o Repositório de Consultas não estiver ativo durante um determinado período, os gráficos ficarão vazios quando a Análise de Desempenho de Consultas exibir esse intervalo de tempo. Será possível habilitar o Repositório de Consultas a qualquer momento, se não estiver em execução. Para obter mais informações, consulte [Melhores práticas com Repositório de Consultas](/sql/relational-databases/performance/best-practice-with-the-query-store).
>

Para obter recomendações de desempenho do banco de dados, selecione [Recomendações](database-advisor-implement-performance-recommendations.md) na folha de navegação da Análise de Desempenho de Consultas.

![A guia Recomendações](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Examinar as consultas que mais consomem CPU

Por padrão, a Análise de Desempenho de Consultas mostra as cinco principais consultas que consomem CPU ao abrir pela primeira vez.

1. Selecione ou limpe as consultas individuais para incluí-las ou excluí-las do gráfico, usando as caixas de seleção.

   A linha superior mostra a porcentagem geral da DTU para o banco de dados. As barras mostram a porcentagem da CPU que as consultas selecionadas consumiram durante o intervalo selecionado. Por exemplo, se **Semana passada** estiver selecionada, cada barra representará um único dia.

   ![Principais consultas](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > A linha da DTU mostrada é agregada a um valor máximo de consumo em períodos de uma hora. É destinada a uma comparação de alto nível apenas com estatísticas de execução de consulta. Em alguns casos, a utilização da DTU pode parecer muito alta em comparação com as consultas executadas, mas esse pode não ser o caso.
   >
   > Por exemplo, se uma consulta aumentou a DTU para 100% por alguns minutos, a linha da DTU na Análise de Desempenho de Consultas mostrará a hora inteira de consumo como 100% (a consequência do valor agregado máximo).
   >
   > Para uma comparação mais precisa (até um minuto), considere criar um gráfico de utilização da DTU personalizado:
   >
   > 1. No portal do Azure, selecione monitoramento **do banco de dados SQL do Azure**  >  **Monitoring**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+Adicionar gráfico**.
   > 4. Selecione a porcentagem da DTU no gráfico.
   > 5. Além disso, selecione **Últimas 24 horas** no menu superior esquerdo e altere para um minuto.
   >
   > Use o gráfico de DTU personalizado com um nível mais refinado de detalhes para comparar com o gráfico de execução da consulta.

   A grade inferior mostra informações agregadas para as consultas visíveis:

   * ID da consulta, que é um identificador exclusivo para a consulta no banco de dados.
   * CPU por consulta durante um intervalo observável, que depende da função de agregação.
   * Duração por consulta, que também depende da função de agregação.
   * Número total de execuções para uma consulta específica.

2. Se os dados ficarem obsoletos, selecione o botão **Atualizar**.

3. Use controles deslizantes e botões de zoom para alterar o intervalo de observação e investigar picos de consumo:

   ![Controles deslizantes e botões de zoom para alterar o intervalo](./media/query-performance-insight-use/zoom.png)

4. Opcionalmente, é possível selecionar a guia **Personalizar** para personalizar o modo de exibição:

   * Métrica (CPU, duração, contagem de execuções).
   * Intervalo de tempo (últimas 24 horas, semana passada ou mês passado).
   * Número de consultas.
   * Função de agregação.
  
   ![Guia Personalizar](./media/query-performance-insight-use/custom-tab.png)
  
5. Selecione o botão **Ir >** para ver a exibição personalizada.

   > [!IMPORTANT]
   > A Análise de Desempenho de Consultas está limitada a exibir de 5 a 20 consultas mais frequentes, dependendo da seleção. O banco de dados pode executar muito mais consultas além das principais exibidas, e essas consultas não serão incluídas no gráfico.
   >
   > Pode haver um tipo de carga de trabalho do banco de dados no qual várias consultas menores, além das principais exibidas, são executadas com frequência e usam a maioria das DTUs. Essas consultas não aparecem no gráfico de desempenho.
   >
   > Por exemplo, uma consulta pode ter consumido uma quantidade substancial de DTU por um tempo, embora o consumo total no período observado seja menor que as outras consultas que consomem mais. Nesse caso, a utilização de recursos dessa consulta não apareceria no gráfico.
   >
   > Se for necessário reconhecer as principais execuções de consultas além das limitações da Análise de Desempenho de Consultas, considere usar a [Análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md) para monitoramento avançado de desempenho de banco de dados e solução de problemas.
   >

## <a name="view-individual-query-details"></a>Exibir detalhes da consulta individual

Para exibir detalhes da consulta:

1. Selecione qualquer consulta na lista das principais consultas.

    ![Lista de principais consultas](./media/query-performance-insight-use/details.png)

   Uma exibição detalhada é aberta. Ela mostra o consumo de CPU, a duração e contagem de execuções ao longo do tempo.

2. Selecione os recursos do gráfico para obter detalhes.

   * O gráfico superior mostra uma linha com a porcentagem geral da DTU do banco de dados. As barras são a porcentagem da CPU que a consulta selecionada consumiu.
   * O segundo gráfico mostra a duração total da consulta selecionada.
   * O gráfico inferior mostra o número total de execuções pela consulta selecionada.

   ![Detalhes da consulta](./media/query-performance-insight-use/query-details.png)

3. Opcionalmente, use controles deslizantes, use botões de zoom ou selecione **Configurações** para personalizar como os dados da consulta serão exibidos, ou para escolher um intervalo de tempo diferente.

   > [!IMPORTANT]
   > A Análise de Desempenho de Consultas não captura consultas DDL. Em alguns casos, pode não capturar todas as consultas ad hoc.
   >

## <a name="review-top-queries-per-duration"></a>Analise as principais consultas por duração

Duas métricas na Análise de Desempenho de Consultas podem ajudá-lo a encontrar possíveis gargalos: duração e contagem de execuções.

Consultas de longa execução tem o maior potencial para bloquear recursos por mais tempo, bloqueando outros usuários e limitando a escalabilidade. Elas também são as melhores candidatas para otimização.

Para identificar consultas de execução longa:

1. Abra a guia **Personalizar** na Análise de Desempenho de Consultas do banco de dados selecionado.
2. Altere as métricas para **duração**.
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione a função de agregação:

   * **Soma** adiciona todo o tempo de execução da consulta para todo o intervalo de observação.
   * **Máximo** localiza consultas em que o tempo de execução foi máximo para todo o intervalo de observação.
   * **Média** localiza o tempo médio de execução de todas as execuções da consulta e mostra as principais para essas médias.

   ![Duração da consulta](./media/query-performance-insight-use/top-duration.png)

5. Selecione o botão **Ir >** para ver a exibição personalizada.

   > [!IMPORTANT]
   > Ajustar a exibição de consulta não atualiza a linha da DTU. A linha da DTU sempre mostra o valor máximo de consumo para o intervalo.
   >
   > Para reconhecer o consumo da DTU do banco de dados com mais detalhes (até um minuto), considere a criação de um gráfico personalizado no portal do Azure:
   >
   > 1. Selecione **monitoramento do banco de dados SQL do Azure**  >  **Monitoring**.
   > 2. Selecione **Métricas**.
   > 3. Selecione **+Adicionar gráfico**.
   > 4. Selecione a porcentagem da DTU no gráfico.
   > 5. Além disso, selecione **Últimas 24 horas** no menu superior esquerdo e altere para um minuto.
   >
   > É recomendável usar o gráfico de DTU personalizado para comparar com o gráfico de desempenho da consulta.
   >

## <a name="review-top-queries-per-execution-count"></a>Analise as principais consultas por contagem de execução

Um aplicativo de usuário que usa o banco de dados pode ficar lento, mesmo que um número alto de execuções possa não estar afetando o próprio banco de dados e o uso de recursos seja baixo.

Em alguns casos, uma alta contagem de execuções pode levar a mais viagens de ida e volta da rede. Viagens de ida e volta e afetam o desempenho. Elas estão sujeitas à latência da rede e à latência do servidor downstream.

Por exemplo, muitos sites controlados por dados acessam o banco de dados para todas as solicitações de usuários. Embora o pool de conexões ajude, o aumento do tráfego de rede e da carga de processamento no servidor pode reduzir o desempenho. Em geral, mantenha as viagens de ida e volta ao mínimo.

Para identificar consultas executadas com frequência ("informativas"):

1. Abra a guia **Personalizar** na Análise de Desempenho de Consultas do banco de dados selecionado.
2. Altere as métricas para **contagem de execuções**.
3. Selecione o número de consultas e o intervalo de observação.
4. Selecione o botão **Ir >** para ver a exibição personalizada.

   ![Contagem de execuções da consulta](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Reconhecer anotações de ajuste de desempenho

Ao explorar a carga de trabalho na Análise de Desempenho de Consultas, você poderá observar ícones com uma linha vertical na parte superior do gráfico.

Esses ícones são anotações. Eles mostram recomendações de desempenho de [Assistente do banco de dados SQL do Azure](database-advisor-implement-performance-recommendations.md). Ao passar o cursor sobre uma anotação, você poderá obter informações resumidas sobre as recomendações de desempenho.

   ![Anotação da consulta](./media/query-performance-insight-use/annotation.png)

Se você quiser saber mais ou aplicar a recomendação do assistente, selecione o ícone para abrir detalhes da ação recomendada. Se essa for uma recomendação ativa, você poderá aplicá-la imediatamente no portal.

   ![Detalhes da anotação da consulta](./media/query-performance-insight-use/annotation-details.png)

Em alguns casos, devido ao nível de zoom, é possível que as anotações próximas umas das outras sejam recolhidas em uma única anotação. A Análise de Desempenho de Consultas representa isso como um ícone de anotação de grupo. Ao selecionar o ícone de anotação de grupo, uma nova folha que lista as anotações é aberta.

Correlacionar consultas e ações de ajuste de desempenho pode ajudá-lo a reconhecer melhor a carga de trabalho.

## <a name="optimize-the-query-store-configuration"></a>Otimizar a configuração de Repositório de Consultas

Ao usar a Análise de Desempenho de Consultas, você poderá ver as seguintes mensagens de erro do Repositório de Consultas:

* "O Repositório de Consulta não está corretamente configurado neste banco de dados. Clique aqui para saber mais."
* "O Repositório de Consulta não está corretamente configurado neste banco de dados. Clique aqui para alterar as configurações."

Essas mensagens geralmente aparecem quando o Repositório de Consultas não pode coletar novos dados.

O primeiro caso ocorre quando o Repositório de Consultas está no estado somente leitura e os parâmetros são definidos de forma ideal. É possível corrigir isso, aumentando o tamanho do armazenamento de dados ou limpando o Repositório de Consultas. (Se você limpar o Repositório de Consultas, toda a telemetria coletada anteriormente será perdida.)

   ![Detalhes do Repositório de Consultas](./media/query-performance-insight-use/qds-off.png)

O segundo caso ocorre quando o Repositório de Consultas não está habilitado ou os parâmetros não estão definidos de maneira ideal. É possível alterar a política de captura e retenção, e também habilitar o Repositório de Consultas, executando os seguintes comandos fornecidos pelo [SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms) ou pelo portal do Azure.

### <a name="recommended-retention-and-capture-policy"></a>Política recomendada de retenção e captura

Há dois tipos de política de retenção:

* **Baseado em tamanho**: se essa política for definida como **automática**, ela limpará os dados automaticamente quando o tamanho máximo for atingido.
* **Baseado em tempo**: por padrão, essa política é definida como 30 dias. Se o Repositório de Consultas ficar sem espaço, ele excluirá informações de consulta com mais de 30 dias.

É possível definir a política de captura para:

* **Todos**: repositório de consultas captura todas as consultas.
* **Automático**: repositório de consultas ignora consultas e consultas incomuns com duração de compilação e execução insignificante. Limites para contagem de execuções, duração de compilação e duração de runtime são determinados internamente. Essa é a opção padrão.
* **Nenhum**: repositório de consultas interrompe a captura de novas consultas, mas as estatísticas de tempo de execução para consultas já capturadas ainda são coletadas.

É recomendável que você defina todas as políticas para **AUTO** e a política de limpeza para 30 dias, executando os seguintes comandos do [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) ou do portal do Azure. (Substitua `YourDB` pelo nome do banco de dados.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Aumente o tamanho do Repositório de Consultas, conectando um banco de dados por meio do [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) ou do portal do Azure e executando a consulta a seguir. (Substitua `YourDB` pelo nome do banco de dados.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

A aplicação dessas configurações fará com que o Repositório de Consultas colete a telemetria para novas consultas. Se for necessário que o Repositório de Consultas fique operacional imediatamente, é possível optar por limpar o Repositório de Consultas executando a seguinte consulta por meio do SSMS ou do portal do Azure. (Substitua `YourDB` pelo nome do banco de dados.)

> [!NOTE]
> A execução da consulta a seguir excluirá toda a telemetria monitorada anteriormente coletada no Repositório de Consultas.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Próximas etapas

Considere o uso de [análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md) para o monitoramento avançado de desempenho de uma grande frota de bancos de dados individuais e em pool, pools elásticos, instâncias gerenciadas e bancos de dados de instância.