---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Sugar CRM | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sugar CRM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.openlocfilehash: c875c89d5b565e95fccaceebdccad82da75b238e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521910"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Sugar CRM

Neste tutorial, você aprenderá a integrar o Sugar CRM ao Azure AD (Azure Active Directory). Ao integrar o Sugar CRM ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Sugar CRM.
* Permitir que os usuários sejam conectados automaticamente ao Sugar CRM com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Sugar CRM habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Sugar CRM dá suporte a SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-sugar-crm-from-the-gallery"></a>Adicionar o Sugar CRM da galeria

Para configurar a integração do Sugar CRM ao Azure AD, você precisará adicionar o Sugar CRM da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria** , digite **Sugar CRM** na caixa de pesquisa.
1. Selecione **Sugar CRM** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Configurar e testar o logon único do Azure AD para o Sugar CRM

Configure e teste o SSO do Azure AD com o Sugar CRM usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Sugar CRM.

Para configurar e testar o SSO do Azure AD com o Sugar CRM, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Sugar CRM](#configure-sugar-crm-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Sugar CRM](#create-sugar-crm-test-user)** – para ter um equivalente de B.Fernandes no Sugar CRM que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Sugar CRM** , localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único** , escolha **SAML**.
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de logon** , digite uma URL usando o seguinte padrão:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. Na caixa de texto **URL de Resposta** , digite uma URL usando o seguinte padrão:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate [equipe de suporte ao cliente do Sugar CRM](https://support.sugarcrm.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Sugar CRM** , copie as URLs apropriadas conforme suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder a ela o acesso ao Sugar CRM.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Sugar CRM**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir**.

## <a name="configure-sugar-crm-sso"></a>Configurar SSO do Sugar CRM

1. Em outra janela do navegador da Web, entre em seu site de empresa do Sugar CRM como administrador.

1. Vá para **Administrador**.

    ![Administrador](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Na seção **Administração** , clique em **Gerenciamento de Senhas**.

    ![A captura de tela mostra a seção Administração, em que você pode selecionar o Gerenciamento de Senhas.](./media/sugarcrm-tutorial/ic795889.png "Administração")

1. Selecione **Habilitar Autenticação SAML**.

    ![A captura de tela mostra a opção para selecionar a Autenticação SAML.](./media/sugarcrm-tutorial/ic795890.png "Administração")

1. Na seção **Autenticação SAML** , realize as seguintes etapas:

    ![Autenticação SAML](./media/sugarcrm-tutorial/ic795891.png "Autenticação do SAML")  

    a. Na caixa de texto **URL de Logon** , cole o valor da **URL de Logon** copiado do portal do Azure.
  
    b. Na caixa de texto **URL de SLO** , cole o valor da **URL de Logoff** copiado do portal do Azure.
  
    c. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole todo o Certificado na caixa de texto **Certificado X.509** .
  
    d. Clique em **Save** (Salvar).

### <a name="create-sugar-crm-test-user"></a>Criar um usuário de teste do Sugar CRM

Para permitir que os usuários do Azure AD entrem no Sugar CRM, eles precisam ser provisionados no Sugar CRM. No caso do SugarCRM, o provisionamento será uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do **Sugar CRM** como administrador.

1. Vá para **Administrador**.

    ![Administrador](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Na seção **Administração** , clique em **Gerenciamento de Usuários**.

    ![A captura de tela mostra a seção Administração, em que você pode selecionar o Gerenciamento de Usuário.](./media/sugarcrm-tutorial/ic795893.png "Administração")

1. Vá para **Usuários \> Criar Novo Usuário**.

    ![Criar Novo Usuário](./media/sugarcrm-tutorial/ic795894.png "Criar novo usuário")

1. Na guia **Perfil do Usuário** , realize as seguintes etapas:

    ![A captura de tela mostra a guia Perfil do Usuário, em que você pode inserir os valores descritos.](./media/sugarcrm-tutorial/ic795895.png "Novo Usuário")

    * Digite o **nome de usuário** , o **sobrenome** e o **endereço de email** de um usuário válido do Azure Active Directory nas caixas de texto relacionadas.
  
1. Para **Status** , selecione **Ativo**.

1. Na guia Senha, execute as seguintes etapas:

    ![A captura de tela mostra a guia Senha, em que você pode inserir os valores descritos.](./media/sugarcrm-tutorial/ic795896.png "Novo Usuário")

    a. Digite a senha na caixa de texto relacionada.

    b. Clique em **Save** (Salvar).

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Sugar CRM ou as APIs fornecidas pelo Sugar CRM para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Sugar CRM no Painel de Acesso, você deverá ser conectado automaticamente ao Sugar CRM, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Sugar CRM com o Azure AD](https://aad.portal.azure.com/)