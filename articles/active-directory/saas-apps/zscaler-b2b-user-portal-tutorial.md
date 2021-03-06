---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Portal do Usuário do Zscaler B2B | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Portal do Usuário do Zscaler B2B.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: jeedes
ms.openlocfilehash: f6b7fb79b5aab1ee3ea6b5b710c766c1a34b099c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519802"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler-b2b-user-portal"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Portal do Usuário do Zscaler B2B

Neste tutorial, você aprenderá a integrar o Portal do Usuário do Zscaler B2B ao Azure AD (Azure Active Directory). Ao integrar o Portal do Usuário do Zscaler B2B ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Portal do Usuário do Zscaler B2B.
* Permitir que os usuários sejam conectados automaticamente ao Portal do Usuário do Zscaler B2B com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Portal do Usuário do Zscaler B2B habilitada para SSO (logon único).

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Portal do Usuário do Zscaler B2B é compatível com o SSO iniciado por **IDP**

* O Portal do Usuário do Zscaler B2B é compatível com o provisionamento de usuário **Just In Time**

* Após configurar o Portal do Usuário do Zscaler B2B, você poderá impor o Controle de Sessão, que protege contra a exportação e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-zscaler-b2b-user-portal-from-the-gallery"></a>Adicionar o Portal do Usuário do Zscaler B2B da galeria

Para configurar a integração do Portal do Usuário do Zscaler B2B ao Azure AD, é necessário adicionar o Portal do Usuário do Zscaler B2B da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar da galeria** , digite **Portal do Usuário do Zscaler B2B** na caixa de pesquisa.
1. Selecione **Portal do Usuário do Zscaler B2B** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler-b2b-user-portal"></a>Configurar e testar o logon único do Azure AD para o Portal do Usuário do Zscaler B2B

Configure e teste o SSO do Azure AD com o Portal do Usuário do Zscaler B2B usando um usuário de teste chamado **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Portal do Usuário do Zscaler B2B.

Para configurar e testar o SSO do Azure AD com o Portal do Usuário do Zscaler B2B, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Portal do Usuário do Zscaler B2B](#configure-zscaler-b2b-user-portal-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Portal do Usuário do Zscaler B2B](#create-zscaler-b2b-user-portal-test-user)** – para ter um equivalente de B.Fernandes no Portal do Usuário do Zscaler B2B que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Portal do Usuário do Zscaler B2B** , localize a seção **Gerenciar** e selecione **logon único** .
1. Na página **Selecionar um método de logon único** , escolha **SAML** .
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML** , insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://samlsp.private.zscaler.com/auth/metadata/<UniqueID>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://samlsp.private.zscaler.com/auth/login?domain=EXAMPLE`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Portal do Usuário do Zscaler B2B](https://help.zscaler.com/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Portal do Usuário do Zscaler B2B** , copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários** .
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha** .
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Portal do Usuário do Zscaler B2B.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, selecione **Portal do Usuário do Zscaler B2B** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

## <a name="configure-zscaler-b2b-user-portal-sso"></a>Configurar o SSO do Portal do Usuário do Zscaler B2B

1. Abra uma nova janela do navegador da Web e entre no site da empresa do Portal do Usuário do Zscaler B2B como administrador e siga estas etapas:

1. No menu à esquerda, clique em **Administração** , navegue até a seção **AUTENTICAÇÃO** e clique em **Configuração de IdP** .

    ![Administração do Administrador do Zscaler Private Access](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-administration.png)

1. No canto superior direito, clique em **Adicionar Configuração de IdP** . 

    ![idp do Administrador do Zscaler Private Access](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-idp.png)

1. Na página **Adicionar Configuração de IdP** , execute as seguintes etapas:
 
    ![seleção do Administrador do Zscaler Private Access](./media/zscaler-b2b-user-tutorial/tutorial-zscaler-private-access-select.png)

    a. Clique em **Selecionar Arquivo** para carregar o arquivo de metadados baixado do Azure AD no campo **Upload de Arquivo de Metadados de IdP** .

    b. Ele lê os **metadados de IdP** do Azure AD e popula todas as informações dos campos, conforme mostrado abaixo.

    ![configuração do Administrador do Zscaler Private Access](./media/zscaler-b2b-user-tutorial/config.png)

    c. Selecione seu domínio do campo **Domínios** .
    
    d. Clique em **Save** (Salvar).

### <a name="create-zscaler-b2b-user-portal-test-user"></a>Criar um usuário de teste do Portal do Usuário do Zscaler B2B

Nesta seção, uma usuária chamada Brenda Fernandes será criada no Portal do Usuário do Zscaler B2B. O Portal do Usuário do Zscaler B2B é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Portal do Usuário do Zscaler B2B, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Portal do Usuário do Zscaler B2B no Painel de Acesso, você deverá ser conectado automaticamente ao Portal do Usuário do Zscaler B2B para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Portal do Usuário do Zscaler B2B com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)