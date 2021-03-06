---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao OpenAthens | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o OpenAthens.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: fc2d1c5dca4265bc0f0c26dd0a6c62a7e1698a38
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621460"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-openathens"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao OpenAthens

Neste tutorial, você aprende a integrar o OpenAthens ao Azure AD (Azure Active Directory). Com a integração do OpenAthens ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao OpenAthens.
* Permitir que os usuários sejam conectados automaticamente ao OpenAthens com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do OpenAthens habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O OpenAthens dá suporte ao SSO iniciado por **IDP**
* O OpenAthens dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-openathens-from-the-gallery"></a>Adicionar o OpenAthens da galeria

Para configurar a integração do OpenAthens ao Azure AD, você precisará adicionar o OpenAthens da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **OpenAthens** na caixa de pesquisa.
1. Selecione **OpenAthens** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-openathens"></a>Configurar e testar logon único do Azure AD para o OpenAthens

Configure e teste o SSO do Azure AD com o OpenAthens usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do OpenAthens.

Para configurar e testar o SSO do Azure AD com o OpenAthens, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do OpenAthens](#configure-openathens-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do OpenAthens](#create-openathens-test-user)** – para ter um equivalente de B. Fernandes no OpenAthens que esteja vinculado à representação da usuária no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OpenAthens**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, carregue o **Arquivo de metadados do Provedor de Serviços**, cujas etapas são mencionadas mais adiante neste tutorial.

    a. Clique em **Carregar arquivo de metadados**.

    ![carregar metadados do OpenAthens](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    ![Procurar e carregar metadados do OpenAthens](common/browse-upload-metadata.png)

    c. Depois que o arquivo de metadados for carregado com êxito, o valor de **Identificador** será preenchido automaticamente na caixa de texto da seção **Configuração Básica do SAML**:

    ![Informações de logon único de Domínio e URLs do OpenAthens](common/idp-identifier.png)

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o OpenAthens**, copie as URLs apropriadas conforme suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure permitindo a ela acesso ao OpenAthens.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **OpenAthens**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-openathens-sso"></a>Configurar o SSO do OpenAthens

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do OpenAthens como administrador.

1. Selecione **Conexões** na lista na guia **Gerenciamento**.

    ![Uma captura de tela que mostra a página do site da empresa "OpenAthens" com a opção "Conexões" selecionada na guia "Gerenciamento".](./media/openathens-tutorial/tutorial_openathens_application1.png)

1. Selecione **SAML 1.1/2.0** e, em seguida, selecione o botão **Configurar**.

    ![Uma captura de tela que mostra a opção "Selecionar um tipo de sistema de autenticação local". Um caixa de diálogo com a opção "SAML 1.1/2.0" e o botão "Configurar" selecionados.](./media/openathens-tutorial/tutorial_openathens_application2.png)

1. Para adicionar a configuração, selecione o botão **Procurar** para carregar o arquivo .xml de metadados que você baixou do portal do Azure e, em seguida, selecione **Adicionar**.

    ![Uma captura de tela que mostra a opção "Adicionar um sistema de autenticação de SAML". Uma caixa de diálogo com a ação "Procurar" e o botão "Adicionar" selecionados.](./media/openathens-tutorial/tutorial_openathens_application3.png)

1. Execute as seguintes etapas na guia **Detalhes**.

    ![Configurar o logon único](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Em **Mapeamento do nome de exibição**, selecione **Usar atributo**.

    b. Na caixa de texto **Exibir atributo do nome**, insira o valor `http://schemas.microsoft.com/identity/claims/displayname`.

    c. Em **Mapeamento de usuário exclusivo**, selecione **Usar atributo**.

    d. Na caixa de texto **Atributo de usuário exclusivo**, insira o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Em **Status**, marque todas as três caixas de seleção.

    f. Em **Criar contas locais**, selecione **automaticamente**.

    g. Selecione **Salvar alterações**.

    h. Na guia **</> Terceira Parte Confiável**, copie a **URL de Metadados** e abra isso no navegador para baixar o arquivo **XML de metadados do SP**. Carregue esse arquivo de metadados do SP na seção **Configuração Básica do SAML** no Azure AD.

    ![Uma captura de tela que mostra a guia "Terceira parte confiável" selecionada e a opção "URL de metadados" realçada.](./media/openathens-tutorial/tutorial_openathens_application5.png)

### <a name="create-openathens-test-user"></a>Criar um usuário de teste do OpenAthens

Nesta seção, um usuário chamado Brenda Fernandes será criado no OpenAthens. O OpenAthens dá suporte ao **provisionamento de usuário Just-In-Time**, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no OpenAthens, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do OpenAthens no Painel de Acesso, você deverá ser conectado automaticamente ao OpenAthens, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o OpenAthens com o Azure AD](https://aad.portal.azure.com/)
