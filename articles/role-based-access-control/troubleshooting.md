---
title: Résoudre des problèmes liés au contrôle d’accès en fonction du rôle pour les ressources Azure | Microsoft Docs
description: Résoudre des problèmes liés au contrôle d’accès en fonction du rôle (RBAC) pour les ressources Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 5429ebb611f852f7672d89de190ddd68dbcb01cf
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707777"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Résoudre des problèmes liés au contrôle d’accès en fonction du rôle pour les ressources Azure

Cet article répond aux questions fréquentes sur le contrôle d’accès en fonction du rôle pour les ressources Azure, afin que vous sachiez à quoi vous attendre lorsque vous utilisez les rôles sur le Portail Azure et que vous puissiez résoudre les problèmes d’accès.

## <a name="problems-with-rbac-role-assignments"></a>Problèmes liés aux attributions de rôle RBAC

- Si vous ne pouvez pas ajouter d’attribution de rôle dans le portail Azure sur **Contrôle d’accès (IAM)** car l’option **Ajouter** > **Ajouter une attribution de rôle** est désactivée, ou parce que vous obtenez l’erreur d’autorisations « Le client avec l’ID d’objet n’est pas autorisé à effectuer l’action », vérifiez que vous êtes actuellement connecté avec un utilisateur disposant d’un rôle qui a l’autorisation `Microsoft.Authorization/roleAssignments/write`, comme [Propriétaire](built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator) dans l’étendue sur laquelle vous essayez d’attribuer le rôle.
- Si vous obtenez le message d’erreur « Plus aucune attribution de rôle ne peut être créée (code : RoleAssignmentLimitExceeded) » lorsque vous tentez d’attribuer un rôle, essayez de réduire le nombre d’attributions de rôles en attribuant plutôt des rôles à des groupes. Azure prend en charge jusqu’à **2 000** attributions de rôle par abonnement. Cette limite d’attribution de rôle est fixe et ne peut pas être augmentée.

## <a name="problems-with-custom-roles"></a>Problèmes liés aux rôles personnalisés

- Si vous avez besoin de connaître les étapes permettant de créer un rôle personnalisé, consultez les tutoriels de rôle personnalisé en utilisant [Azure PowerShell](tutorial-custom-role-powershell.md) ou [Azure CLI](tutorial-custom-role-cli.md).
- Si vous ne parvenez pas à mettre à jour un rôle personnalisé existant, vérifiez que vous êtes actuellement connecté avec un utilisateur disposant d’un rôle qui a l’autorisation `Microsoft.Authorization/roleDefinition/write`, comme [Propriétaire](built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](built-in-roles.md#user-access-administrator).
- Si vous ne pouvez pas supprimer un rôle personnalisé et que vous obtenez le message d’erreur « Certaines attributions de rôle existantes font référence au rôle (code : RoleDefinitionHasAssignments) », cela signifie que des attributions de rôle utilisent toujours le rôle personnalisé. Supprimez ces attributions de rôle et réessayez de supprimer ce rôle.
- Si vous obtenez le message d’erreur « La limite de définition de rôle a été dépassée. Plus aucune définition de rôle ne peut être créée (code : RoleDefinitionLimitExceeded) » quand vous essayez de créer un rôle personnalisé, supprimez tous les rôles personnalisés qui ne sont pas utilisés. Azure prend en charge jusqu’à **5 000** rôles personnalisés dans un locataire. (Pour les clouds spécialisés, tels que Azure Government, Azure Allemagne et Azure China 21Vianet, la limite s’élève à 2 000 rôles personnalisés.)
- Si vous obtenez une erreur similaire à « Le client a l’autorisation d’effectuer l’action 'Microsoft.Authorization/roleDefinitions/write' sur l’étendue '/subscriptions/{subscriptionid}'. Cependant, l’abonnement lié est introuvable » quand vous essayez de mettre à jour un rôle personnalisé, vérifiez si une ou plusieurs [étendues attribuables](role-definitions.md#assignablescopes) ont été supprimées dans le locataire. Si l’étendue a été supprimée, créez un ticket de support, car il n’existe aucune solution en libre-service disponible pour l’instant.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Récupérer le contrôle d’accès en fonction du rôle (RBAC) lorsque les abonnements sont déplacés entre les locataires

- Si vous avez besoin de connaître les étapes permettant de transférer un abonnement à un locataire Azure AD différent, consultez [Transférer la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md).
- Si vous transférez un abonnement vers un autre locataire Azure AD, toutes les attributions de rôle définies sont définitivement supprimées du locataire Azure AD source et ne sont pas migrées sur le locataire Azure AD cible. Vous devez recréer vos attributions de rôle dans le locataire cible. Vous devez également recréer manuellement les identités managées pour les ressources Azure. Pour plus d’informations, consultez [Questions fréquentes et problèmes connus en lien avec les identités managées](../active-directory/managed-identities-azure-resources/known-issues.md).
- Si vous êtes administrateur général Azure AD et que vous n’avez pas accès à un abonnement après son déplacement entre des locataires, utilisez l’option **Gestion de l’accès pour les ressources Azure** afin d’[élever votre accès](elevate-access-global-admin.md) temporairement et ainsi accéder à l’abonnement.

## <a name="issues-with-service-admins-or-co-admins"></a>Problèmes liés aux coadministrateurs ou administrateurs de service

- Si vous rencontrez des problèmes avec l’administrateur ou les coadministrateurs du service, consultez [Ajouter ou modifier les administrateurs d’abonnements Azure](../billing/billing-add-change-azure-subscription-administrator.md) et [Rôles d’administrateur d’abonnement classique, rôles RBAC Azure et rôles d’administrateur Azure AD](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Accès refusé ou erreurs d’autorisations

- Si vous obtenez l’erreur d’autorisations « Le client avec l’ID d’objet n’est pas autorisé à effectuer l’action sur l’étendue (code : AuthorizationFailed) » lorsque vous tentez de créer une ressource, vérifiez que vous êtes actuellement connecté avec un utilisateur disposant d’un rôle qui a l’autorisation d’écriture sur la ressource au niveau de l’étendue sélectionnée. Par exemple, pour gérer les machines virtuelles dans un groupe de ressources, vous devez disposer du rôle [Contributeur de machines virtuelles](built-in-roles.md#virtual-machine-contributor) sur le groupe de ressources (ou l’étendue parente). Afin d’obtenir la liste des autorisations pour chaque rôle intégré, consultez [Rôles intégrés pour les ressources Azure](built-in-roles.md).
- Si vous obtenez l’erreur d’autorisations « Vous n’êtes pas autorisé à créer une demande de support » quand vous tentez de créer ou de mettre à jour un ticket de support, vérifiez que vous êtes actuellement connecté avec un utilisateur disposant d’un rôle qui a l’autorisation `Microsoft.Support/supportTickets/write`, comme [Collaborateur de la demande de support](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Attributions de rôles avec un principal de sécurité inconnu

Si vous attribuez un rôle à un principal de sécurité (utilisateur, groupe, principal du service ou identité gérée), puis que vous supprimez ensuite ce principal de sécurité sans supprimer l’attribution de rôle, le type de principal de sécurité pour l’attribution de rôle porte la mention **Inconnu**. La capture d’écran suivante montre un exemple dans le Portail Azure. Le nom du principal de sécurité porte les mentions **Identité supprimée** et **L’identité n’existe plus**. 

![Groupe de ressources d'application web](./media/troubleshooting/unknown-security-principal.png)

Si vous répertoriez cette attribution de rôle à l’aide d’Azure PowerShell, `DisplayName` est vide à l’écran et `ObjectType` est défini sur Inconnu. Par exemple, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) retourne une attribution de rôle similaire à ce qui suit :

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

De même, si vous répertoriez cette attribution de rôle à l’aide d’Azure CLI, `principalName` est vide à l’écran. Par exemple, [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) retourne une attribution de rôle similaire à ce qui suit :

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Il n’est pas gênant de conserver ces attributions de rôle, mais vous pouvez les supprimer à l’aide d’étapes similaires aux autres attributions de rôle. Pour plus d’informations sur la suppression des attributions de rôles, consultez [Portail Azure](role-assignments-portal.md#remove-a-role-assignment), [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment) ou [Azure CLI](role-assignments-cli.md#remove-a-role-assignment)

Dans PowerShell, si vous essayez de supprimer les attributions de rôles à l’aide de l’ID d’objet et du nom de définition de rôle alors que plusieurs attributions de rôle correspondent à vos paramètres, le message d’erreur suivant s’affiche : « Les informations fournies ne correspondent pas à une attribution de rôle ». Voici un exemple du message d’erreur :

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Si ce message d’erreur s’affiche, prenez soin de spécifier également les paramètres `-Scope` ou `-ResourceGroupName`.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>Les changements de contrôle d’accès en fonction du rôle ne sont pas détectés

Azure Resource Manager met parfois en cache des données et des configurations pour améliorer les performances. Lors de la création ou de la suppression d’attributions de rôles, un délai de 30 minutes maximum peut être nécessaire avant que les modifications soient prises en compte. Si vous utilisez le portail Azure, Azure PowerShell ou Azure CLI, vous pouvez forcer une actualisation de vos modifications d’attribution de rôle en vous déconnectant et en vous reconnectant. Si vous apportez des modifications d’attribution de rôle à l’aide d’appels d’API REST, vous pouvez forcer une actualisation en actualisant votre jeton d’accès.

## <a name="web-app-features-that-require-write-access"></a>Fonctionnalités d’application web qui nécessitent un accès en écriture

Si vous accordez un accès utilisateur en lecture seule à une seule application web, certaines fonctionnalités sont désactivées, ce que vous n’avez peut-être pas prévu. Les fonctionnalités de gestion suivantes exigent un accès en **écriture** à une application web (Collaborateur ou Propriétaire) et ne sont pas disponibles en lecture seule.

* Commandes (comme start, stop, etc.)
* Modification de paramètres tels que la configuration générale, les paramètres de mise à l’échelle, les paramètres de sauvegarde et les paramètres d’analyse
* Accès aux informations d’identification de publication et autres informations secrètes, telles que les paramètres d’application et les chaînes de connexion
* Diffusion de journaux d’activité
* Configuration des journaux de diagnostic
* Console (invite de commandes)
* Déploiements actifs et récents (pour le déploiement continu Git local)
* Estimation de dépense
* Tests web
* Réseau virtuel (accessible à un lecteur uniquement si un réseau virtuel a été précédemment configuré par un utilisateur avec accès en écriture).

Si vous ne pouvez accéder à aucune de ces vignettes, vous devez obtenir l’accès Collaborateur à l’application web auprès de votre administrateur.

## <a name="web-app-resources-that-require-write-access"></a>Ressources d’application web qui nécessitent un accès en écriture

Les applications web sont compliqués par la présence de quelques ressources différentes qui interagissent. Voici un groupe de ressources classique avec plusieurs sites web :

![Groupe de ressources d'application web](./media/troubleshooting/website-resource-model.png)

En conséquence, si vous accordez à un utilisateur le seul accès à l’application web, la plupart des fonctionnalités du volet Site web dans le portail Azure sont désactivées.

Les éléments suivants requièrent l’accès **en écriture** au **plan App Service** qui correspond à votre site web :  

* Affichage du niveau tarifaire de l’application web (Gratuit ou Standard)  
* Configuration de mise à l'échelle (le nombre d'instances, la taille de la machine virtuelle, les paramètres de mise à l'échelle automatique)  
* Quotas (stockage, bande passante, UC)  

Les éléments suivants requièrent l’accès **en écriture** à l’ensemble du **Groupe de ressources** qui contient le site web :  

* Certificats et liaisons SSL (les certificats SSL peuvent être partagés entre différents sites dans le même groupe de ressources et emplacement)  
* Règles d'alerte  
* Paramètres de mise à l'échelle automatique  
* Composants Application Insights  
* Tests web  

## <a name="virtual-machine-features-that-require-write-access"></a>Fonctionnalités de machine virtuelle qui nécessitent un accès en écriture

Comme pour les applications web, certaines fonctionnalités du volet de la machine virtuelle exigent un accès en écriture à la machine virtuelle ou à d’autres ressources du groupe de ressources.

Les machines virtuelles sont associées aux noms de domaine, réseaux virtuels, comptes de stockage et règles d'alerte.

Les éléments suivants requièrent l’accès **en écriture** à la **machine virtuelle** :

* Points de terminaison  
* Adresses IP  
* Disques  
* Extensions  

Les éléments suivants requièrent l’accès **en écriture** à la **machine virtuelle**, ainsi qu’au **Groupe de ressources** (avec le nom de domaine) auxquels ils appartiennent :  

* Groupe à haute disponibilité  
* Jeu d'équilibrage de la charge  
* Règles d'alerte  

Si vous ne pouvez accéder à aucune de ces vignettes, demandez l’accès Collaborateur au groupe de ressources à votre administrateur.

## <a name="azure-functions-and-write-access"></a>Azure Functions et accès en écriture

Certaines fonctionnalités de [Azure Functions](../azure-functions/functions-overview.md) nécessitent un accès en écriture. Par exemple, si un utilisateur est assigné au rôle [Lecteur](built-in-roles.md#reader), il ne peut pas voir les fonctions au sein d’une application de fonction. Le portail affiche **(aucun accès)** .

![Aucun accès aux applications de fonction](./media/troubleshooting/functionapps-noaccess.png)

Un lecteur peut cliquer sur l’onglet **Fonctionnalités de plateforme**, puis cliquez sur **Tous les paramètres** pour afficher certains paramètres liés à une application de fonction (semblable à une application Web), mais il ne peut pas modifier ces paramètres. Pour accéder à ces fonctionnalités, vous aurez besoin du rôle [Contributeur](built-in-roles.md#contributor).

## <a name="next-steps"></a>Étapes suivantes

- [Résolution des problèmes pour les utilisateurs invités](role-assignments-external-users.md#troubleshoot)
- [Gérer l’accès aux ressources Azure à l’aide du contrôle RBAC et du portail Azure](role-assignments-portal.md)
- [Afficher les journaux d’activité des changements de contrôle d’accès en fonction du rôle pour les ressources Azure](change-history-report.md)

