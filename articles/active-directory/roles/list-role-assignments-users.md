---
title: Répertorier les attributions de rôles Azure AD d’un utilisateur – Azure Active Directory
description: Découvrez comment répertorier les attributions de rôles Azure AD d’un utilisateur.
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
ms.openlocfilehash: 58647d37da29066c14457ae6624763c800e6f123
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776415"
---
# <a name="list-azure-ad-role-assignments-for-a-user"></a>Répertorier les attributions de rôle Azure AD d’un utilisateur

Un rôle peut être attribué à un utilisateur directement ou de manière transitive via un groupe. Cet article explique comment répertorier les rôles Azure AD attribués à un utilisateur. Pour plus d’informations sur l’attribution de rôles à des groupes, consultez [Utiliser des groupes Azure AD pour gérer les attributions de rôles](groups-concept.md).

## <a name="prerequisites"></a>Prérequis

- Module AzureADPreview (avec PowerShell)
- Module Microsoft.Graph (avec PowerShell)
- Consentement administrateur lors de l’utilisation de l’Afficheur Graph pour l’API Microsoft Graph

Pour plus d’informations, consultez [Prérequis pour utiliser PowerShell ou de l’Afficheur Graph](prerequisites.md).

## <a name="azure-portal"></a>Portail Azure
Procédez comme suit pour répertorier les rôles Azure AD d’un utilisateur à l’aide du portail Azure. Votre expérience sera différente selon que vous avez activé ou non [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure AD](https://aad.portal.azure.com).

2. Sélectionnez **Azure Active Directory** > **Utilisateurs** > *Nom d’utilisateur* > **Rôles attribués**.

    Vous pouvez voir la liste des rôles attribués à l’utilisateur dans différentes étendues. En outre, vous pouvez voir si le rôle a été attribué directement ou par le biais d’un groupe.
    
    ![Liste des rôles attribués à un utilisateur dans Portail Azure](./media/list-role-assignments-users/list-role-definition.png)

    Si vous disposez d’une licence Premium P2, vous verrez l’expérience PIM, qui contient les détails les attributions de rôle admissibles, actives et expirées.

    ![Liste des rôles attribués à un utilisateur dans PIM](./media/list-role-assignments-users/list-role-definition-pim.png)

## <a name="powershell"></a>PowerShell

Procédez comme suit pour répertorier les rôles Azure AD attribués à un utilisateur à l’aide de PowerShell.

1. Installez les modules AzureADPreview et Microsoft.Graph à l’aide de la commande [Install-module](/powershell/azure/active-directory/install-adv2).
  
    ```powershell
    Install-module -name AzureADPreview
    Install-module -name Microsoft.Graph
    ```
  
2. Ouvrez une fenêtre PowerShell et utilisez [Import-Module](/powershell/module/microsoft.powershell.core/import-module) pour importer le module AzureADPreview. Pour plus d'informations, consultez [Prérequis pour utiliser PowerShell ou de l'Afficheur Graph](prerequisites.md).

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

3. Dans une fenêtre PowerShell, utilisez [Connect-AzureAD](/powershell/module/azuread/connect-azuread) pour vous connecter à votre locataire.

    ```powershell
    Connect-AzureAD
    ```
4. Utilisez [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment) pour obtenir les rôles attribués directement à un utilisateur.

    ```powershell
    #Get the user
    $userId = (Get-AzureADUser -Filter "userPrincipalName eq 'alice@contoso.com'").ObjectId

    #Get direct role assignments to the user
    $directRoles = (Get-AzureADMSRoleAssignment -Filter "principalId eq '$userId'").RoleDefinitionId
    ```

5. Pour obtenir les rôles transitifs attribués à l’utilisateur, utilisez les cmdlets suivantes.
  
    a. Utilisez [Get-AzureADMSGroup](/powershell/module/azuread/get-azureadmsgroup) pour obtenir la liste de tous les groupes auxquels des rôles peuvent être attribués.  
  
      ```powershell
      $roleAssignableGroups = (Get-AzureADMsGroup -All $true | Where-Object IsAssignableToRole -EQ 'True').Id
      ```

    b. Utilisez [Connect-MgGraph](/graph/powershell/get-started) pour vous connecter et utiliser les cmdlets PowerShell de Microsoft Graph.
  
      ```powershell
      Connect-MgGraph -Scopes "User.Read.All”
      ```
  
    c. Utilisez l’API [checkMemberObjects](/graph/api/user-checkmemberobjects) pour déterminer de quels groupes à attribution de rôle l’utilisateur est membre. 
    
      ```powershell
      $uri = "https://graph.microsoft.com/beta/directoryObjects/$userId/microsoft.graph.checkMemberObjects"

      $userRoleAssignableGroups = (Invoke-MgGraphRequest -Method POST -Uri $uri -Body @{"ids"= $roleAssignableGroups}).value
      ```
  
    d. Utilisez [Get-AzureADMSRoleAssignment](/powershell/module/azuread/get-azureadmsroleassignment) pour parcourir en boucle les groupes et obtenir les rôles qui leur sont attribués.
  
      ```powershell
      $transitiveRoles=@()
      foreach($item in $userRoleAssignableGroups){
          $transitiveRoles += (Get-AzureADMSRoleAssignment -Filter "principalId eq '$item'").RoleDefinitionId
      }
      ```

6. Combinez les attributions de rôle directes et transitives de l’utilisateur.
  
    ```powershell
    $allRoles = $directRoles + $transitiveRoles
    ```
  
## <a name="microsoft-graph-api"></a>API Microsoft Graph

Procédez comme suit pour répertorier les rôles Azure AD attribués à un utilisateur à l’aide de l’API Microsoft Graph dans [Graph Explorer](https://aka.ms/ge).

1. Connectez-vous à [Afficheur Graph](https://aka.ms/ge).

1. Utilisez l’API [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) pour obtenir les rôles attribués directement à un utilisateur. Ajoutez la requête suivante à l’URL et sélectionnez **Exécuter la requête**.

   ```HTTP
   GET https://graph.microsoft.com/beta/rolemanagement/directory/roleAssignments?$filter=principalId eq '55c07278-7109-4a46-ae60-4b644bc83a31'
   ```
  
3. Pour obtenir les rôles transitifs attribués à l’utilisateur, procédez comme suit.

    a. Utilisez [List groups](/graph/api/group-list) pour obtenir la liste de tous les groupes auxquels des rôles peuvent être attribués.
  
      ```HTTP
      GET https://graph.microsoft.com/beta/groups?$filter=isAssignableToRole eq true 
      ```
  
    b. Transmettez cette liste à l’API [checkMemberObjects](/graph/api/user-checkmemberobjects) pour déterminer de quels groupes à attribution de rôle l’utilisateur est membre. 
    
      ```HTTP
      POST https://graph.microsoft.com/beta/users/55c07278-7109-4a46-ae60-4b644bc83a31/checkMemberObjects
      {
          "ids": [
              "936aec09-47d5-4a77-a708-db2ff1dae6f2",
              "5425a4a0-8998-45ca-b42c-4e00920a6382",
              "ca9631ad-2d2a-4a7c-88b7-e542bd8a7e12",
              "ea3cee12-360e-411d-b0ba-2173181daa76",
              "c3c263bb-b796-48ee-b4d2-3fbc5be5f944"
          ]
      }
      ```
  
    c. Utilisez l’API [List roleAssignments](/graph/api/rbacapplication-list-roleassignments) pour parcourir en boucle les groupes et obtenir les rôles qui leur sont attribués.
  
      ```HTTP
      GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId eq '5425a4a0-8998-45ca-b42c-4e00920a6382' 
      ```

## <a name="next-steps"></a>Étapes suivantes

* [Lister les attributions de rôle Azure AD](view-assignments.md).
* [Attribuer des rôles Azure AD aux utilisateurs](manage-roles-portal.md).
* [Attribuer des rôles Azure AD aux groupes](groups-assign-role.md)
