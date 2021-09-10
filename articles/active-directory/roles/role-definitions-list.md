---
title: Répertorier les définitions de rôles Azure AD – Azure AD
description: Apprenez à répertorier les rôles intégrés et personnalisés d’Azure.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 07/23/2021
ms.author: rolyon
ms.reviewer: absinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c5131cb56ff65b6c559186cf491c4367ecbc7d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525202"
---
# <a name="list-azure-ad-role-definitions"></a>Répertorier les définitions de rôles Azure AD

Une définition de rôle est une collection d’autorisations qui peuvent être effectuées, comme lire, écrire et supprimer. Elle est généralement simplement appelée « rôle ». Azure Active Directory compte plus de 60 rôles intégrés. Vous pouvez également créer vos propres rôles personnalisés. Si vous vous êtes déjà demandé « À quoi ces rôles servent-ils vraiment ? », vous pouvez voir une liste détaillée des autorisations pour chacun des rôles.

Cet article explique comment répertorier les rôles intégrés et personnalisés Azure AD, ainsi que leurs autorisations.

## <a name="prerequisites"></a>Prérequis

- Module AzureADPreview (avec PowerShell)
- Consentement administrateur (avec l’Afficheur Graph pour l’API Microsoft Graph)

Pour plus d’informations, consultez [Prérequis pour utiliser PowerShell ou de l’Afficheur Graph](prerequisites.md).

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [centre d’administration Azure AD](https://aad.portal.azure.com).

1. Sélectionnez **Azure Active Directory** > **Rôles et administrateurs** pour afficher la liste de tous les rôles disponibles.

    ![liste des rôles sur le portail Azure](./media/role-definitions-list/view-roles-in-azure-active-directory.png)

1. Sur la droite, cliquez sur le bouton de sélection, puis sur **Description** pour afficher la liste complète des autorisations pour un rôle.

    Cette page inclut des liens vers la documentation correspondante pour vous guider tout au long de la gestion des rôles.

    ![Capture d'écran représentant la page « Administrateur général - Description ».](./media/role-definitions-list/role-description-updated.png)

## <a name="powershell"></a>PowerShell

Effectuez les étapes suivantes pour répertorier les rôles Azure AD à l’aide de PowerShell.

1. Ouvrez une fenêtre PowerShell et utilisez [Import-Module](/powershell/module/microsoft.powershell.core/import-module) pour importer le module AzureADPreview. Pour plus d'informations, consultez [Prérequis pour utiliser PowerShell ou de l'Afficheur Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

2. Dans une fenêtre PowerShell, utilisez [Connect-AzureAD](/powershell/module/azuread/connect-azuread) pour vous connecter à votre locataire.

    ```powershell
    Connect-AzureAD
    ```
3. Utilisez [Get-AzureADMSRoleDefinition](/powershell/module/azuread/get-azureadmsroledefinition) pour obtenir tous les rôles.

    ```powershell
    Get-AzureADMSRoleDefinition
    ```

4. Pour afficher la liste des autorisations d’un rôle, utilisez la cmdlet suivante.
    
    ```powershell
    # Do this avoid truncation of the list of permissions
    $FormatEnumerationLimit = -1
    
    (Get-AzureADMSRoleDefinition -Filter "displayName eq 'Conditional Access Administrator'").RolePermissions | Format-list
    ```

## <a name="microsoft-graph-api"></a>API Microsoft Graph

Suivez ces instructions pour répertorier les rôles Azure AD à l’aide de l’API Microsoft Graph dans [Afficheur Graph](https://aka.ms/ge).

1. Connectez-vous à l’[Afficheur Graph](https://aka.ms/ge).
2. Sélectionnez **GET** en tant que méthode HTTP dans la liste déroulante. 
3. Sélectionnez la version de l’API sur **bêta**.
4. Ajoutez la requête suivante pour utiliser l’API [List roleDefinitions](/graph/api/rbacapplication-list-roledefinitions).

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
   ```

5. Sélectionnez **Exécuter la requête** pour répertorier les rôles.
6. Pour afficher les autorisations d’un rôle, utilisez l’API suivante.

   ```HTTP
   GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=DisplayName eq 'Conditional Access Administrator'&$select=rolePermissions
   ```

## <a name="next-steps"></a>Étapes suivantes

* [Répertorier les attributions de rôle Azure AD](view-assignments.md).
* [Attribuer des rôles Azure AD aux utilisateurs](manage-roles-portal.md).
* [Rôles intégrés Azure AD](permissions-reference.md).
