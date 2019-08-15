---
title: Afficher les autorisations de rôle d’administrateur dans le centre d’administration – Azure Active Directory | Microsoft Docs
description: Vous pouvez désormais afficher et gérer les membres d’un rôle d’administrateur Azure AD dans le centre d’administration Azure AD.
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
ms.openlocfilehash: bf45d45481712e30d40bacec9a3c4d80d1ed56b6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707430"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Afficher les attributions de rôles personnalisées dans Azure Active Directory

Cet article explique comment afficher les rôles personnalisés que vous avez affectés dans Azure Active Directory (Azure AD). Dans Azure Active Directory (Azure AD), les rôles peuvent être affectés au niveau du répertoire ou au niveau d’une seule application. Les attributions de rôles au niveau du répertoire sont ajoutées à la liste des attributions de rôle d’application uniques, mais les attributions de rôles au niveau de l’application uniquement ne sont pas ajoutées à la liste des attributions de niveau répertoire.

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-the-azure-ad-portal"></a>Afficher les attributions d’un rôle au niveau du répertoire à l’aide du portail Azure AD

1. Connectez-vous au  [Centre d’administration Azure AD](https://aad.portal.azure.com)  à l’aide des autorisations administrateur de rôle privilégié ou administrateur d’entreprise dans l’organisation Azure AD.
1. Sélectionnez **Azure Active Directory**, sélectionnez  **Rôles et administrateurs**, puis sélectionnez un rôle pour l’ouvrir et afficher ses propriétés.
1. Sélectionnez **Affectations** pour afficher les affectations du rôle.

    ![Affichez les affectations de rôle et les autorisations lorsque vous ouvrez un rôle dans la liste](./media/roles-view-assignments/role-assignments.png)

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-azure-ad-powershell"></a>Affichez les affectations d’un rôle au niveau du répertoire à l’aide du portail Azure AD PowerShell

Vous pouvez automatiser l’affectation des rôles Azure AD aux comptes utilisateurs en utilisant Azure PowerShell. Cet article utilise le module [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles).

### <a name="prepare-powershell"></a>Préparer PowerShell

Vous devez tout d’abord [télécharger le module de préversion Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

Pour installer le module PowerShell Azure AD, utilisez les commandes suivantes :

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

Pour vérifier que le module est prêt à l’emploi, utilisez la commande suivante :

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>Afficher les affectations d’un rôle

Exemple d’affichage des affectations d’un rôle.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-the-assignments-of-a-role-with-directory-scope-using-microsoft-graph-api"></a>Afficher les attributions d’un rôle au niveau du répertoire à l’aide de l’API Microsoft Graph

Requête HTTP pour obtenir une attribution de rôle pour une définition de rôle donnée

GET

``` HTTP
https://graph.windows.net/<tenantDomain-or-tenantId>/roleAssignments?api-version=1.61-internal&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

response

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview"></a>Afficher les attributions d’un rôle au niveau d’une seule application à l’aide du portail Azure AD (préversion)

1. Connectez-vous au  [Centre d’administration Azure AD](https://aad.portal.azure.com)  à l’aide des autorisations administrateur de rôle privilégié ou administrateur d’entreprise dans l’organisation Azure AD.
1. Sélectionnez Azure Active Directory, puis **Inscriptions d’applications**, et sélectionnez l’inscription d’application pour afficher ses propriétés. Vous devrez peut-être sélectionner **Toutes les applications** pour afficher la liste complète des inscriptions d’applications dans votre organisation Azure AD.

    ![Créer ou modifier des inscriptions d’applications à partir de la page Inscriptions d’applications](./media/roles-create-custom/appreg-all-apps.png)

1. Sélectionnez  **Rôles et administrateurs**, puis sélectionnez un rôle pour afficher ses propriétés.

    ![Afficher les attributions de rôle d’inscription d’application à partir de la page Inscriptions d’applications](./media/roles-view-assignments/appreg-assignments.png)

1. Sélectionnez **Affectations** pour afficher les affectations du rôle.

    ![Afficher les attributions de rôle d’inscription d’application à partir des propriétés d’une inscription d’application](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>Étapes suivantes

* N’hésitez pas à nous donner votre avis sur le [forum des rôles d’administrateur Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Pour plus d’informations sur les rôles et l’attribution de rôle d’administrateur, consultez [Attribuer des rôles d’administrateur](directory-assign-admin-roles.md).
* Pour les autorisations d’utilisateur par défaut, consultez une [comparaison des autorisations par défaut d’un utilisateur invité et d’un membre](../fundamentals/users-default-permissions.md).
