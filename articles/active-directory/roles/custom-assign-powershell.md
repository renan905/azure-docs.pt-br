---
title: Atribuir funções personalizadas usando o Azure PowerShell-Azure AD | Microsoft Docs
description: Gerenciar membros de uma função personalizada de administrador do Azure AD com o Azure PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4695d0844ef8b707edce53a05de611c91223a46
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861945"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Atribuir funções personalizadas com o escopo de recurso usando o PowerShell no Azure Active Directory

Este artigo descreve como criar uma atribuição de função em escopo de toda a organização no Azure Active Directory (AD do Azure). Atribuir uma função em escopo de toda a organização concede acesso na organização do Azure AD. Para criar uma atribuição de função com um escopo de um único recurso do Azure AD, consulte [como criar uma função personalizada e atribuí-la no escopo do recurso](custom-create.md). Este artigo usa o módulo [Azure Active Directory PowerShell versão 2](/powershell/module/azuread/#directory_roles) .

Para obter mais informações sobre as funções de administrador do Azure AD, consulte [atribuindo funções de administrador no Azure Active Directory](permissions-reference.md).

## <a name="required-permissions"></a>Permissões necessárias

Conecte-se à sua organização do Azure AD usando uma conta de administrador global para atribuir ou remover funções.

## <a name="prepare-powershell"></a>Preparar o PowerShell

Instale o módulo do PowerShell do Azure AD do [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de versão prévia do Azure AD PowerShell usando o seguinte comando:

``` PowerShell
import-module azureadpreview
```

Para verificar se o módulo está pronto para uso, a versão retornada pelo seguinte comando deve coincidir com o comando listado aqui:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Agora você pode começar a usar os cmdlets do módulo. Para obter uma descrição completa dos cmdlets no módulo do Azure AD, consulte a documentação de referência online para o [módulo de visualização do Azure ad](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Atribuir uma função a um usuário ou a uma entidade de serviço com escopo de recurso

1. Abra o módulo PowerShell de visualização do Azure AD.
1. Entre executando o comando `Connect-AzureAD` .
1. Crie uma nova função usando o seguinte script do PowerShell.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Para atribuir a função a uma entidade de serviço em vez de um usuário, use o [cmdlet Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal).

## <a name="operations-on-roledefinition"></a>Operações no RoleDefinition

Os objetos de definição de função contêm a definição da função interna ou personalizada, juntamente com as permissões concedidas por essa atribuição de função. Esse recurso exibe as definições de função personalizadas e directoryRoles internas (que são exibidas na forma equivalente roleDefinition). Hoje, uma organização do Azure AD pode ter, no máximo, 30 RoleDefinitions personalizados exclusivos definidos.

### <a name="create-operations-on-roledefinition"></a>Criar operações no RoleDefinition

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Operações de leitura no RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Operações de atualização no RoleDefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Operações de exclusão no RoleDefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Operações no RoleAssignment

As atribuições de função contêm informações vinculando uma determinada entidade de segurança (uma entidade de serviço de usuário ou aplicativo) a uma definição de função. Se necessário, você pode adicionar um escopo de um único recurso do Azure AD para as permissões atribuídas.  A restrição do escopo de permissões tem suporte para funções internas e personalizadas.

### <a name="create-operations-on-roleassignment"></a>Criar operações no RoleAssignment

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-operations-on-roleassignment"></a>Operações de leitura no RoleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Operações de exclusão no RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Próximas etapas

- Compartilhar conosco no fórum de [funções administrativas do Azure ad](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para obter mais informações sobre funções e atribuições de função de administrador do Azure AD, consulte [atribuir funções de administrador](permissions-reference.md)
- Para permissões de usuário padrão, consulte uma [comparação de permissões de usuário de convidado e membro padrão](../fundamentals/users-default-permissions.md)
