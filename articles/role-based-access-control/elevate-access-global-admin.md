---
title: Élever l’accès d’un administrateur général dans Azure Active Directory | Microsoft Docs
description: Cet article décrit comment élever l’accès d’un administrateur général dans Azure Active Directory à l’aide du portail Azure ou d’une API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a2f66078a817f5e6ad7296df11634a1a6130a055
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321663"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Élever l’accès d’un administrateur général dans Azure Active Directory

Si vous êtes un [administrateur général](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) dans Azure Active Directory (Azure AD), il peut vous arriver de vouloir effectuer les opérations suivantes :

- récupérer l’accès à un abonnement Azure quand un utilisateur a perdu cet accès ;
- accorder à un autre utilisateur ou à vous-même l’accès à un abonnement Azure ;
- afficher tous les abonnements Azure au sein d’une organisation ;
- autoriser une application d’automation (telle qu’une application de facturation ou d’audit) à accéder à tous les abonnements Azure.

Cet article décrit les différentes façons dont vous pouvez élever votre accès dans Azure AD.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="overview"></a>Vue d’ensemble

Les ressources Azure AD et Azure sont sécurisées de façon indépendante les unes des autres. Ainsi, les attributions de rôles Azure AD n’accordent pas d’accès aux ressources Azure et inversement, les attributions de rôles Azure n’accordent pas d’accès à Azure AD. En revanche, si vous êtes administrateur général dans Azure AD, vous pouvez vous attribuer à vous-même un accès à tous les abonnements et groupes d’administration Azure de votre annuaire. Utilisez cette fonctionnalité si vous n’avez pas accès aux ressources de l’abonnement Azure, comme les machines virtuelles ou les comptes de stockage, et que vous voulez utiliser vos privilèges d’administrateur général pour accéder à ces ressources.

Quand vous élevez votre accès, le rôle [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) vous est attribué dans Azure au niveau de l’étendue racine (`/`). Ceci vous permet de voir toutes les ressources et d’attribuer des accès dans n’importe quel abonnement ou groupe d’administration de l’annuaire. Les attributions de rôle Administrateur de l’accès utilisateur peuvent être supprimées avec PowerShell.

Vous devez supprimer cet accès avec élévation de privilèges après avoir effectué les modifications nécessaires au niveau de l’étendue racine.

![Élever l’accès](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Portail Azure

Effectuez les étapes suivantes pour élever l’accès d’un administrateur général à l’aide du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com) en tant qu’administrateur général.

1. Dans la liste de navigation, cliquez sur **Azure Active Directory**, puis sur **Propriétés**.

   ![Propriétés Azure AD - capture d’écran](./media/elevate-access-global-admin/aad-properties.png)

1. Sous **Gestion des accès aux ressources Azure**, définissez le commutateur sur **Oui**.

   ![Gestion des accès aux ressources Azure - capture d’écran](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Quand vous définissez le commutateur sur **Oui**, le rôle Administrateur de l’accès utilisateur vous est attribué dans Azure RBAC au niveau de l’étendue racine (/). Ceci vous accorde l’autorisation d’attribuer des rôles dans tous les abonnements et groupes d’administration Azure associés à cet annuaire Azure AD. Ce commutateur est disponible seulement pour les utilisateurs auxquels le rôle Administrateur général a été attribué dans Azure AD.

   Quand vous définissez le commutateur sur **Non**, le rôle Administrateur de l’accès utilisateur dans Azure RBAC est supprimé de votre compte d’utilisateur. Vous ne pouvez plus attribuer des rôles dans tous les abonnements et groupes d’administration Azure associés à cet annuaire Azure AD. Vous pouvez voir et gérer seulement les abonnements et groupes d’administration Azure auxquels l’accès vous a été accordé.

1. Cliquez sur **Enregistrer** pour enregistrer votre paramètre.

   Ce paramètre n’est pas une propriété globale et s’applique uniquement à l’utilisateur actuellement connecté.

1. Effectuez les tâches que vous devez accomplir via un accès avec élévation de privilèges. Lorsque vous avez terminé, repositionnez le commutateur sur **Non**.

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-role-assignment-at-the-root-scope-"></a>Répertorier une attribution de rôle dans l’étendue racine (/)

Pour répertorier l’attribution de rôle Administrateur de l’accès utilisateur pour un utilisateur dans l’étendue racine (`/`), utilisez la commande [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Supprimer une attribution de rôle dans l’étendue racine (/)

Pour supprimer l’attribution de rôle Administrateur des accès utilisateur d’un utilisateur dans l’étendue racine (`/`), utilisez la commande [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment).

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="rest-api"></a>API REST

### <a name="elevate-access-for-a-global-administrator"></a>Élever l’accès d’un administrateur général

Pour élever l’accès d’un administrateur général à l’aide de l’API REST, suivez les étapes de base suivantes.

1. Avec REST, appelez `elevateAccess`, qui vous accorde le rôle Administrateur de l’accès utilisateur dans l’étendue racine (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Créez une [attribution de rôle](/rest/api/authorization/roleassignments) pour attribuer le rôle de votre choix quelle que soit l’étendue. L’exemple suivant montre les propriétés pour l’attribution du rôle {roleDefinitionID} dans l’étendue racine (`/`) :

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. En tant qu’Administrateur des accès utilisateur, vous pouvez également supprimer des attributions de rôles dans l’étendue racine (`/`).

1. Révoquez vos privilèges d’Administrateur des accès utilisateur jusqu’à ce que vous en ayez de nouveau besoin.

### <a name="list-role-assignments-at-the-root-scope-"></a>Répertorier les attributions de rôles dans l’étendue racine (/)

Vous pouvez lister toutes les attributions de rôles d’un utilisateur dans l’étendue racine (`/`).

- Appelez [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope), où `{objectIdOfUser}` est l’ID objet de l’utilisateur dont vous souhaitez récupérer les attributions de rôles.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Lister les affectations de refus dans l’étendue racine (/)

Vous pouvez répertorier toutes les affectations de refus d’un utilisateur dans l’étendue racine (`/`).

- Appelez GET denyAssignments où `{objectIdOfUser}` est l’ID objet de l’utilisateur dont vous souhaitez récupérer les affectations de refus.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Supprimer l’accès élevé

Lorsque vous appelez `elevateAccess`, vous créez une attribution de rôle pour vous-même. Pour révoquer ces privilèges, vous devez donc supprimer l’attribution.

1. Appelez [GET roleDefinitions](/rest/api/authorization/roledefinitions/get), où `roleName` est l’Administrateur de l’accès utilisateur, pour déterminer l’ID de nom du rôle Administrateur de l’accès utilisateur.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Enregistrez l’ID à partir du paramètre `name`, en l’occurrence `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Vous devez également lister les attributions de rôles pour l’administrateur d’annuaire au niveau de l’annuaire. Listez toutes les attributions dans l’étendue de l’annuaire pour le `principalId` de l’administrateur d’annuaire qui a effectué l’appel d’élévation d’accès. Ceci liste toutes les attributions de l’annuaire pour l’objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Un administrateur d’annuaire ne doit normalement pas avoir beaucoup d’attributions. Si la requête précédente retourne un trop grand nombre d’attributions, vous pouvez aussi interroger toutes les attributions seulement au niveau de l’étendue de l’annuaire, puis filtrer les résultats : `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Les appels précédents retournent une liste des attributions de rôle. Recherchez l’attribution de rôle pour laquelle l’étendue est `"/"`, où `roleDefinitionId` se termine par l’ID du nom de rôle trouvé à l’étape 1 et où `principalId` correspond à l’objectid de l’administrateur d’annuaire. 
    
    Exemple d’attribution de rôle :

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Une fois encore, enregistrez l’ID du paramètre `name`, en l’occurrence e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Enfin, utilisez l’ID d’attribution de rôle pour supprimer l’attribution ajoutée par `elevateAccess` :

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Étapes suivantes

- [Comprendre les différents rôles](rbac-and-directory-admin-roles.md)
- [Contrôle d’accès en fonction du rôle avec REST](role-assignments-rest.md)
