---
title: Élever l’accès d’un administrateur général dans Azure Active Directory | Microsoft Docs
description: Cet article décrit comment élever l’accès d’un administrateur général dans Azure Active Directory à l’aide du portail Azure ou d’une API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: b671ff6b473093e59bce18c7bf98b32e9849bbb0
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077205"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Élever l’accès d’un administrateur général dans Azure Active Directory

Si vous êtes un [administrateur général](../active-directory/active-directory-assign-admin-roles-azure-portal.md#global-administrator) dans Azure Active Directory (Azure AD), il peut vous arriver de vouloir effectuer les opérations suivantes :

- récupérer l’accès à un abonnement Azure quand un utilisateur a perdu cet accès ;
- accorder à un autre utilisateur ou à vous-même l’accès à un abonnement Azure ;
- afficher tous les abonnements Azure au sein d’une organisation ;
- autoriser une application d’automation (telle qu’une application de facturation ou d’audit) à accéder à tous les abonnements Azure.

Par défaut, les rôles d’administrateur Azure AD et les rôles de contrôle d’accès en fonction du rôle (RBAC) ne couvrent pas Azure AD et Azure. En revanche, si vous êtes un administrateur général dans Azure AD, vous pouvez élever votre accès afin de gérer des abonnements et des groupes d’administration Azure. Lorsque vous élevez votre accès, le rôle [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) (un rôle RBAC) vous est attribué pour tous les abonnements d’un client particulier. Le rôle Administrateur de l’accès utilisateur vous permet d’accorder à d’autres utilisateurs l’accès aux ressources Azure figurant dans l’étendue racine (`/`).

Cette élévation doit être temporaire et n’avoir lieu qu’en cas de nécessité.

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Élever l’accès d’un administrateur général à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) ou au [Centre d’administration Azure Active Directory](https://aad.portal.azure.com).

1. Dans la liste de navigation, cliquez sur **Azure Active Directory**, puis sur **Propriétés**.

   ![Propriétés Azure AD - capture d’écran](./media/elevate-access-global-admin/aad-properties.png)

1. Sous **L’administrateur général peut gérer des abonnements et des groupes d’administration Azure**, positionnez le commutateur sur **Oui**.

   ![L’administrateur général peut gérer des abonnements et des groupes d’administration Azure - capture d’écran](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Lorsque vous positionnez le commutateur sur **Oui**, votre compte d’administrateur général (utilisateur actuellement connecté) est ajouté au rôle Administrateur de l’accès utilisateur dans RBAC Azure dans l’étendue racine (`/`), ce qui vous donne accès à tous les abonnements Azure associés à votre client Azure AD pour les consulter et générer des rapports à leur sujet.

   Lorsque vous positionnez le commutateur sur **Non**, votre compte d’administrateur général (utilisateur actuellement connecté) est supprimé du rôle Administrateur de l’accès utilisateur dans RBAC Azure. Vous ne pouvez pas voir tous les abonnements Azure associés au client Azure AD, et ne pouvez afficher et gérer que les abonnements Azure auxquels vous avez été autorisé à accéder.

1. Cliquez sur **Enregistrer** pour enregistrer votre paramètre.

   Ce paramètre n’est pas une propriété globale et s’applique uniquement à l’utilisateur actuellement connecté.

1. Effectuez les tâches que vous devez accomplir via un accès avec élévation de privilèges. Lorsque vous avez terminé, repositionnez le commutateur sur **Non**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Répertorier une attribution de rôle dans l’étendue racine (/) à l’aide de PowerShell

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

## <a name="delete-a-role-assignment-at-the-root-scope--using-powershell"></a>Supprimer une attribution de rôle dans l’étendue racine (/) à l’aide de PowerShell

Pour supprimer une attribution de rôle Administrateur de l’accès utilisateur pour un utilisateur dans l’étendue racine (`/`), utilisez la commande [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment).

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Élever l’accès d’un administrateur général à l’aide de l’API REST

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

1. En tant qu’Administrateur de l’accès utilisateur, vous pouvez également supprimer des attributions de rôles dans l’étendue racine (`/`).

1. Révoquez vos privilèges d’Administrateur de l’accès utilisateur jusqu’à ce que vous en ayez de nouveau besoin.


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>Annuler l’action elevateAccess avec l’API REST

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

2. Vous devez également répertorier l’attribution de rôle pour l’administrateur client dans l’étendue du client. Répertoriez toutes les attributions dans l’étendue du client pour le `principalId` de l’administrateur client qui a effectué l’appel d’élévation d’accès. Cela a pour effet de répertorier toutes les attributions dans le client pour l’objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Un administrateur client ne doit pas avoir beaucoup d’attributions. Si la requête précédente retourne un trop grand nombre d’attributions, vous pouvez aussi interroger toutes les attributions au niveau de l’étendue du client uniquement, puis filtrer les résultats : `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. Les appels précédents retournent une liste des attributions de rôle. Recherchez l’attribution de rôle où l’étendue est « / », `roleDefinitionId` se termine par l’ID du nom du rôle trouvé à l’étape 1, et `principalId` correspond à l’objectId de l’administrateur client. 
    
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

- [Contrôle d’accès en fonction du rôle avec REST](role-assignments-rest.md)
- [Gérer les attributions d’accès](role-assignments-users.md)
