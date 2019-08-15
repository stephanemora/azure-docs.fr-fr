---
title: Créer une définition de rôle personnalisé dans un contrôle d'accès basé sur un rôle Azure AD – Azure Active Directory | Microsoft Docs
description: Créez des rôles Azure AD personnalisés avec l’étendue des ressources sur les ressources Azure Active Directory.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1166839608c709db9aa052d6d0db5221fa15354
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880745"
---
# <a name="create-a-custom-role-and-assign-at-resource-scope-in-azure-active-directory"></a>Créer un rôle personnalisé et l’affecter à l’étendue des ressources dans Azure Active Directory

Cet article explique comment créer des rôles personnalisés dans Azure Active Directory (Azure AD). Vous pouvez créer des rôles personnalisés sous l’onglet [Rôles et administrateurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) de la page vue d’ensemble de Azure AD. Le rôle peut être affecté soit au niveau de l’étendue au niveau du répertoire, soit à une étendue de ressource d’inscription d’application uniquement.

Pour plus d’informations, consultez [Vue d’ensemble des rôles personnalisés](roles-custom-overview.md) pour les principes de base des rôles personnalisés.

## <a name="using-the-azure-ad-portal"></a>Utilisation du portail Azure AD

### <a name="create-a-new-custom-role-to-grant-access-to-manage-app-registrations"></a>Créer un rôle personnalisé pour accorder l’accès pour gérer les inscriptions des applications

1. Connectez-vous au  [Centre d’administration Azure AD](https://aad.portal.azure.com)  à l’aide des autorisations administrateur de rôle privilégié ou administrateur d’entreprise dans l’organisation Azure AD.
1. Sélectionnez Rôles  **Azure Active Directory** >  **, puis sélectionnez Rôles et administrateurs** > **Nouveau rôle personnalisé**.

   ![Créer ou modifier des rôles à partir de la page Rôles et administrateurs](./media/roles-create-custom/new-custom-role.png)

1. Dans l'onglet **Notions de base**, indiquez un nom et une description pour le rôle, puis cliquez sur **Suivant**.

   ![fournir un nom et une description pour un rôle personnalisé sous l’onglet Notions de base](./media/roles-create-custom/basics-tab.png)

1. Sous l'onglet **Permissions**, sélectionnez les permissions nécessaires pour gérer les propriétés de base et les propriétés des informations d’identification des inscriptions d’applications. Pour obtenir une description détaillée de chaque permission, consultez [Sous-types et permissions d'inscription de l’application dans Azure Active Directory](./roles-custom-available-permissions.md).
   1. Tout d’abord, entrez « informations d’identification » dans la barre de recherche et sélectionnez la permission `microsoft.directory/applications/credentials/update`.

      ![Sélectionnez les permissions pour un rôle personnalisé sous l’onglet Permissions.](./media/roles-create-custom/permissions-tab.png)

   1. Ensuite, entrez « notions de base » dans la barre de recherche, sélectionnez la permission `microsoft.directory/applications/basic/update`, puis cliquez sur **Suivant**.
1. Sur l’onglet **Vérifier + Créer**, vérifiez les permissions, puis sélectionnez **Créer**.

Votre rôle personnalisé s’affiche dans la liste des rôles disponibles à affecter.

## <a name="assign-a-role-scoped-to-a-resource"></a>Affecter un rôle étendu à une ressource

À l’instar des rôles intégrés, les rôles personnalisés peuvent être affectés au niveau de l’étendue de l’organisation pour accorder l’accès à toutes les inscriptions d’applications. Toutefois, des rôles personnalisés peuvent également être affectés au niveau de l’étendue des ressources. Cela vous permet de donner à l’intervenant l’autorisation de mettre à jour les informations d’identification et les propriétés de base d’une application unique sans avoir à créer un deuxième rôle personnalisé.

1. Si ce n’est pas déjà fait, connectez-vous au  [centre d’administration Azure AD](https://aad.portal.azure.com) avec les autorisations de développeur d’applications dans l’organisation Azure AD.
1. Sélectionnez **Inscriptions d’applications**.
1. Sélectionnez l’inscription d’application à laquelle vous accordez l’accès à gérer. Vous devrez peut-être sélectionner **Toutes les applications** pour afficher la liste complète des inscriptions d’applications dans votre organisation Azure AD.

    ![Sélectionner l’inscription d’application en tant qu’étendue de ressource pour une attribution de rôle](./media/roles-create-custom/appreg-all-apps.png)

1. Dans inscription d’application, sélectionnez **Rôles et administrateurs**. Si vous n’en avez encore jamais créé, vous trouverez des instructions dans la [procédure précédente](#create-a-new-custom-role-to-grant-access-to-manage-app-registrations).

1. Sélectionnez le rôle pour ouvrir la page **Affectations**.
1. Sélectionnez **Ajouter une affectation** pour ajouter un utilisateur. Aucune permission n’est accordée à l’utilisateur sur l’inscription d’une application autre que celle sélectionnée.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Créer un rôle personnalisé à l’aide de Azure AD PowerShell

### <a name="prepare-powershell"></a>Préparer PowerShell

Vous devez tout d’abord [télécharger le module Azure AD Preview PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Pour installer le module PowerShell Azure AD, utilisez les commandes suivantes :

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Pour vérifier que le module est prêt à l’emploi, utilisez la commande suivante :

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.2.31     azuread                      {Add-AzureADAdministrati...}
```

### <a name="create-the-custom-role"></a>Créer le rôle personnalisé

Créez un nouveau rôle à l’aide du script PowerShell suivant :

``` PowerShell
# Basic role information
$displayName = "Application Support Administrator"
$description = "Can manage basic aspects of application registrations."
$templateId = (New-Guid).Guid
 
# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/basic/update",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
 
# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Assigner le rôle personnalisé à l’aide de Azure AD PowerShell

Attribuez le rôle à l’aide du script PowerShell ci-dessous :

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

## <a name="create-a-custom-role-using-microsoft-graph-api"></a>Créer un rôle personnalisé à l’aide de l’API Microsoft Graph

1. Créez la définition de rôle.

    Requête HTTP pour créer une définition de rôle personnalisée.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
    ```

    body

    ``` HTTP
   {
       "description": "Can manage basic aspects of application registrations.",
       "displayName": "Application Support Administrator",
       "isEnabled": true,
       "templateId": "<GUID>",
       "rolePermissions": [
           {
               "allowedResourceActions": [
                   "microsoft.directory/applications/basic/update",
                   "microsoft.directory/applications/credentials/update"
               ]
           }
       ]
   }
    ```

1. Créez l’attribution de rôle.

    Requête HTTP pour créer une définition de rôle personnalisée.

    POST

    ``` HTTP
    https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
    ```

    body

    ``` HTTP
   {
       "principalId":"<GUID OF USER>",
       "roleDefinitionId":"<GUID OF ROLE DEFINITION>",
       "resourceScope":"/<GUID OF APPLICATION REGISTRATION>"
   }
    ```

## <a name="next-steps"></a>Étapes suivantes

- N’hésitez pas à nous donner votre avis sur le [forum des rôles d’administrateur Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
- Pour plus d’informations sur les rôles et l’attribution de rôle d’administrateur, consultez [Attribuer des rôles d’administrateur](directory-assign-admin-roles.md).
- Pour les autorisations d’utilisateur par défaut, consultez une [comparaison des autorisations par défaut d’un utilisateur invité et d’un membre](../fundamentals/users-default-permissions.md).
