---
title: Attribuer des rôles Azure AD à différentes étendues – Azure Active Directory
description: Découvrez comment attribuer des rôles à différentes étendues dans Azure Active Directory
services: active-directory
author: abhijeetsinha
manager: vincesm
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 08/12/2021
ms.author: absinh
ms.reviewer: rolyon
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02c9e92d09ce929b47cd9a71559eabb4e7c40858
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532927"
---
# <a name="assign-azure-ad-roles-at-different-scopes"></a>Attribuer des rôles Azure AD à différentes étendues

Cet article explique comment attribuer des rôles Azure AD à différentes étendues. Pour comprendre les étendues dans Azure AD, consultez [Vue d’ensemble du contrôle d’accès en fonction du rôle dans Azure Active Directory](custom-overview.md). En général, vous devez appartenir à l’étendue à laquelle vous souhaitez limiter l’attribution de rôle. Par exemple, si vous souhaitez attribuer un rôle Administrateur de support technique dont l’étendue se limite à une [unité administrative](administrative-units.md), accédez à **Azure AD > Unités administratives > {unité administrative} > Rôles et administrateurs**, puis procédez à l’attribution du rôle. Cela vous permet d’effectuer une attribution de rôle au niveau d’une unité administrative, et non dans l’ensemble du locataire.

## <a name="prerequisites"></a>Configuration requise

- Administrateur de rôle privilégié ou Administrateur général.
- Module AzureADPreview lors de l’utilisation de PowerShell.
- Consentement administrateur lors de l’utilisation de l’Afficheur Graph pour l’API Microsoft Graph.

Pour plus d'informations, consultez [Prérequis pour utiliser PowerShell ou de l'Afficheur Graph](prerequisites.md).

## <a name="assign-roles-scoped-to-the-tenant"></a>Attribuer des rôles au niveau d’un locataire

Cette section explique comment attribuer des rôles au niveau d’un locataire.

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory** > **Rôles et administrateurs** pour afficher la liste de tous les rôles disponibles.

    ![Page Rôles et administrateurs dans Azure Active Directory.](./media/manage-roles-portal/roles-and-administrators.png)

1. Sélectionnez un rôle pour afficher ses attributions. Pour identifier plus facilement le rôle dont vous avez besoin, utilisez **Ajouter des filtres** pour filtrer les rôles.

1. Sélectionnez **Ajouter des attributions**, puis sélectionnez les utilisateurs que vous souhaitez affecter à ce rôle.

    ![Volet Ajouter des attributions pour le rôle sélectionné.](./media/manage-roles-portal/add-assignments.png)

1. Sélectionnez **Ajouter** pour attribuer le rôle.

### <a name="powershell"></a>PowerShell

Effectuez les étapes suivantes pour attribuer des rôles Azure AD à l’aide de PowerShell.

1. Ouvrez une fenêtre PowerShell et utilisez [Import-Module](/powershell/module/microsoft.powershell.core/import-module) pour importer le module AzureADPreview. Pour plus d'informations, consultez [Prérequis pour utiliser PowerShell ou de l'Afficheur Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Dans une fenêtre PowerShell, utilisez [Connect-AzureAD](/powershell/module/azuread/connect-azuread) pour vous connecter à votre locataire.

    ```powershell
    Connect-AzureAD
    ```

1. Utilisez [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) pour obtenir l’utilisateur.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Utilisez [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) pour obtenir le rôle que vous souhaitez attribuer.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Billing Administrator'"
    ```

1. Définissez le locataire comme étendue de l’attribution de rôle.

    ```powershell
    $directoryScope = '/'
    ```

1. Utilisez [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) pour attribuer le rôle.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```
 
### <a name="microsoft-graph-api"></a>API Microsoft Graph

Suivez ces instructions pour attribuer un rôle à l’aide de l’API Microsoft Graph dans [Afficheur Graph](https://aka.ms/ge).

1. Connectez-vous à l’[Afficheur Graph](https://aka.ms/ge).

1. Utilisez l’API [List user](/graph/api/user-list) pour obtenir l’utilisateur.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Utilisez l’API [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) pour obtenir le rôle que vous souhaitez attribuer.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Billing Administrator'
    ```
    
1. Utilisez l’API [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) pour attribuer le rôle.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/"
    }
    ```

## <a name="assign-roles-scoped-to-an-administrative-unit"></a>Attribuer des rôles au niveau d’une unité administrative

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory > Unités administratives** pour voir la liste de toutes les unités administratives.

1. Sélectionnez une unité administrative.

    ![Unités administratives dans Azure Active Directory.](./media/assign-roles-different-scopes/admin-units.png)

1. Sélectionnez **Rôles et administrateurs** dans le menu de navigation gauche pour afficher la liste de tous les rôles qui peuvent être attribués à une unité administrative.

    ![Menu Rôles et administrateurs sous Unités administratives dans Azure Active Directory.](./media/assign-roles-different-scopes/admin-units-roles.png)

1. Sélectionnez le rôle souhaité.

1. Sélectionnez **Ajouter des attributions**, puis sélectionnez les utilisateurs ou le groupe que vous souhaitez attribuer à ce rôle.

1. Sélectionnez **Ajouter** pour attribuer le rôle au niveau de l’unité administrative.

>[!Note] 
>Vous ne verrez pas ici la liste complète des rôles intégrés ou personnalisés Azure AD. Ceci est normal. Nous montrons les rôles qui ont des autorisations liées aux objets pris en charge au sein de l’unité administrative. Reportez-vous à [cette documentation](administrative-units.md) pour obtenir la liste des objets pris en charge au sein d’une unité administrative.

### <a name="powershell"></a>PowerShell

Effectuez les étapes suivantes pour attribuer des rôles Azure AD au niveau de l’unité administrative à l’aide de PowerShell.

1. Ouvrez une fenêtre PowerShell et utilisez [Import-Module](/powershell/module/microsoft.powershell.core/import-module) pour importer le module AzureADPreview. Pour plus d'informations, consultez [Prérequis pour utiliser PowerShell ou de l'Afficheur Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Dans une fenêtre PowerShell, utilisez [Connect-AzureAD](/powershell/module/azuread/connect-azuread) pour vous connecter à votre locataire.

    ```powershell
    Connect-AzureAD
    ```

1. Utilisez [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) pour obtenir l’utilisateur.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Utilisez [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) pour obtenir le rôle que vous souhaitez attribuer.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'User Administrator'"
    ```

1. Utilisez [Get-AzureADMSAdministrativeUnit](/powershell/module/azuread/get-azureadmsadministrativeunit) pour obtenir l’unité administrative au niveau de laquelle vous souhaitez attribuer le rôle.

    ```powershell
    $adminUnit = Get-AzureADMSAdministrativeUnit -Filter "displayName eq 'Seattle Admin Unit'"
    $directoryScope = '/administrativeUnits/' + $adminUnit.Id
    ```

1. Utilisez [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) pour attribuer le rôle.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>API Microsoft Graph

Suivez ces instructions pour attribuer un rôle au niveau de l’unité administrative à l’aide de l’API Microsoft Graph dans [Afficheur Graph](https://aka.ms/ge).

1. Connectez-vous à l’[Afficheur Graph](https://aka.ms/ge).

1. Utilisez l’API [List user](/graph/api/user-list) pour obtenir l’utilisateur.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Utilisez l’API [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) pour obtenir le rôle que vous souhaitez attribuer.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'User Administrator'
    ```
    
1. Utilisez l’API [List administrativeUnits](/graph/api/administrativeunit-list) pour obtenir l’unité administrative au niveau de laquelle vous souhaitez attribuer le rôle.

    ```HTTP
    GET https://graph.microsoft.com/beta/administrativeUnits?$filter=displayName eq 'Seattle Admin Unit'
    ```

1. Utilisez l’API [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) pour attribuer le rôle.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/administrativeUnits/<provide objectId of the admin unit obtained above>"
    }
    ```
    
>[!Note] 
>Ici, directoryScopeId est spécifié en tant que */administrativeUnits/foo* au lieu de */foo*. C’est voulu. L’étendue */administrativeUnits/foo* signifie que le principal peut gérer les membres de l’unité administrative (en fonction du rôle qui lui est attribué), et non l’unité administrative même. L’étendue */foo* signifie que le principal peut gérer cet objet Azure AD. Dans la section suivante, vous verrez que l’étendue est */foo*, car un rôle attribué au niveau d’une inscription d’application accorde le privilège nécessaire pour gérer l’objet.

## <a name="assign-roles-scoped-to-an-app-registration"></a>Attribuer des rôles au niveau d’une inscription d’application

### <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory > Inscriptions d’applications** pour voir la liste de toutes les inscriptions d’applications.

1. Sélectionnez une application. Vous pouvez utiliser la zone de recherche pour trouver l’application souhaitée.

    ![Inscriptions d’applications dans Azure Active Directory.](./media/assign-roles-different-scopes/app-reg.png)

1. Sélectionnez **Rôles et administrateurs** dans le menu de navigation gauche pour afficher la liste de tous les rôles qui peuvent être attribués à une inscription d’application.

    ![Rôles d’une inscription d’application dans Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-roles.png)

1. Sélectionnez le rôle souhaité.

1. Sélectionnez **Ajouter des attributions**, puis sélectionnez les utilisateurs ou le groupe que vous souhaitez attribuer à ce rôle.

    ![Ajoutez des rôles attribués au niveau d’une inscription d’application dans Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-add-assignment.png)

1. Sélectionnez **Ajouter** pour attribuer le rôle au niveau de l’inscription d’application. 

    ![Ajout des rôles attribués au niveau d’une inscription d’application dans Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-assignment.png)
    
    ![Rôle attribué à un utilisateur au niveau d’une inscription d’application dans Azure Active Directory.](./media/assign-roles-different-scopes/app-reg-scoped-assignment.png)
    

>[!Note] 
>Vous ne verrez pas ici la liste complète des rôles intégrés ou personnalisés Azure AD. Ceci est normal. Nous montrons les rôles qui ont des autorisations liées à la gestion des inscriptions d’applications uniquement. 


### <a name="powershell"></a>PowerShell

Effectuez les étapes suivantes pour attribuer des rôles Azure AD au niveau de l’application à l’aide de PowerShell.

1. Ouvrez une fenêtre PowerShell et utilisez [Import-Module](/powershell/module/microsoft.powershell.core/import-module) pour importer le module AzureADPreview. Pour plus d'informations, consultez [Prérequis pour utiliser PowerShell ou de l'Afficheur Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. Dans une fenêtre PowerShell, utilisez [Connect-AzureAD](/powershell/module/azuread/connect-azuread) pour vous connecter à votre locataire.

    ```powershell
    Connect-AzureAD
    ```

1. Utilisez [Get-AzureADUser](/powershell/module/azuread/get-azureaduser) pour obtenir l’utilisateur.

    ```powershell
    $user = Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'"
    ```

1. Utilisez [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) pour obtenir le rôle que vous souhaitez attribuer.

    ```powershell
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Administrator'"
    ```

1. Utilisez [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) pour obtenir l’inscription d’application au niveau de laquelle vous souhaitez attribuer le rôle.

    ```powershell
    $appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
    $directoryScope = '/' + $appRegistration.objectId
    ```

1. Utilisez [New-AzureADMSRoleAssignment](/powershell/module/azuread/new-azureadmsroleassignment) pour attribuer le rôle.

    ```powershell
    $roleAssignment = New-AzureADMSRoleAssignment -DirectoryScopeId $directoryScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
    ```

### <a name="microsoft-graph-api"></a>API Microsoft Graph

Suivez ces instructions pour attribuer un rôle au niveau de l’application à l’aide de l’API Microsoft Graph dans [Afficheur Graph](https://aka.ms/ge).

1. Connectez-vous à l’[Afficheur Graph](https://aka.ms/ge).

1. Utilisez l’API [List user](/graph/api/user-list) pour obtenir l’utilisateur.

    ```HTTP
    GET https://graph.microsoft.com/beta/users?$filter=userPrincipalName eq 'alice@contoso.com'
    ```
    
1. Utilisez l’API [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions) pour obtenir le rôle que vous souhaitez attribuer.

    ```HTTP
    GET https://graph.microsoft.com/beta/rolemanagement/directory/roleDefinitions?$filter=displayName eq 'Application Administrator'
    ```
    
1. Utilisez l’API [List applications](/graph/api/application-list) pour obtenir l’application au niveau de laquelle vous souhaitez attribuer le rôle.

    ```HTTP
    GET https://graph.microsoft.com/beta/applications?$filter=displayName eq 'f/128 Filter Photos'
    ```

1. Utilisez l’API [Create roleAssignments](/graph/api/rbacapplication-post-roleassignments) pour attribuer le rôle.

    ```HTTP
    POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    {
        "principalId": "<provide objectId of the user obtained above>",
        "roleDefinitionId": "<provide templateId of the role obtained above>",
        "directoryScopeId": "/<provide objectId of the app registration obtained above>"
    }
    ```

>[!Note] 
>Ici, directoryScopeId est spécifié en tant que */foo*, contrairement à la section ci-dessus. C’est voulu. L’étendue */foo* signifie que le principal peut gérer cet objet Azure AD. L’étendue */administrativeUnits/foo* signifie que le principal peut gérer les membres de l’unité administrative (en fonction du rôle qui lui est attribué), et non l’unité administrative même.


## <a name="next-steps"></a>Étapes suivantes

* [Lister les attributions de rôle Azure AD](view-assignments.md).
* [Attribuer des rôles Azure AD aux utilisateurs](manage-roles-portal.md).
* [Attribuer des rôles Azure AD aux groupes](groups-assign-role.md)