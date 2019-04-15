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
ms.date: 03/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d85c49cc8533b88382de81f8f12fde7116afb69a
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407587"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Résoudre des problèmes liés au contrôle d’accès en fonction du rôle pour les ressources Azure

Cet article répond aux questions fréquentes sur le contrôle d’accès en fonction du rôle pour les ressources Azure, afin que vous sachiez à quoi vous attendre lorsque vous utilisez les rôles sur le Portail Azure et que vous puissiez résoudre les problèmes d’accès.

## <a name="problems-with-rbac-role-assignments"></a>Problèmes liés aux attributions de rôle RBAC

- Si vous ne pouvez pas ajouter une attribution de rôle dans le portail Azure sur **contrôle d’accès (IAM)** , car le **ajouter** > **ajouter une attribution de rôle** option est désactivée ou Étant donné que vous obtenez l’erreur d’autorisations « le client avec l’id d’objet n’est pas autorisé à effectuer l’action », vérifiez que vous êtes actuellement connecté avec un utilisateur se voit attribuer un rôle qui a le `Microsoft.Authorization/roleAssignments/write` autorisation comme [propriétaire](built-in-roles.md#owner) ou [administrateur des accès utilisateur](built-in-roles.md#user-access-administrator) dans l’étendue que vous voulez attribuer le rôle.
- Si vous obtenez le message d’erreur « aucune attribution de rôle plus ne peut être créée (code : RoleAssignmentLimitExceeded) » lorsque vous essayez d’assigner un rôle, essayez de réduire le nombre d’attributions de rôles en attribuant des rôles à des groupes à la place. Azure prend en charge jusqu’à **2 000** attributions de rôle par abonnement.

## <a name="problems-with-custom-roles"></a>Problèmes liés aux rôles personnalisés

- Si vous avez besoin d’étapes pour savoir comment créer un rôle personnalisé, consultez les didacticiels de rôle personnalisé à l’aide de [Azure PowerShell](tutorial-custom-role-powershell.md) ou [Azure CLI](tutorial-custom-role-cli.md).
- Si vous ne parvenez pas à mettre à jour un rôle personnalisé existant, vérifiez que vous êtes actuellement connecté avec un utilisateur se voit attribuer un rôle qui a le `Microsoft.Authorization/roleDefinition/write` autorisation comme [propriétaire](built-in-roles.md#owner) ou [administrateur des accès utilisateur](built-in-roles.md#user-access-administrator).
- Si vous ne pouvez pas supprimer un rôle personnalisé et obtenez le message d’erreur « Il existe des attributions de rôles référence au rôle (code : RoleDefinitionHasAssignments) », il existe des attributions de rôles que vous utilisez toujours le rôle personnalisé. Supprimer ces attributions de rôles et essayez de supprimer le rôle personnalisé à nouveau.
- Si vous obtenez le message d’erreur « limite de définition de rôle a dépassé. Aucune définition de rôle plus ne peut être créée (code : RoleDefinitionLimitExceeded) » lorsque vous essayez de créer un nouveau rôle personnalisé, supprimez tous les rôles personnalisés qui ne sont pas utilisées. Azure prend en charge jusqu’à **2 000** rôles personnalisés dans un locataire.
- Si vous obtenez une erreur similaire à « le client a l’autorisation d’effectuer l’action 'Microsoft.Authorization/roleDefinitions/write' sur l’étendue « / subscriptions / {subscriptionid} », mais l’abonnement lié est introuvable » lorsque vous essayez de mettre à jour un rôle personnalisé, consultez Si un ou plusieurs [étendues attribuables](role-definitions.md#assignablescopes) ont été supprimés dans le locataire. Si l’étendue a été supprimé, puis créez un ticket de support car il n’existe aucune solution libre-service disponible pour l’instant.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Récupérer le contrôle d’accès en fonction du rôle (RBAC) lorsque les abonnements sont déplacés entre les locataires

- Si vous avez besoin d’étapes pour savoir comment transférer un abonnement à un autre annuaire Azure AD locataire, consultez [transférer la propriété d’un abonnement Azure à un autre compte](../billing/billing-subscription-transfer.md).
- Si vous transférez un abonnement à un autre annuaire Azure AD locataire, toutes les attributions de rôle sont définitivement supprimées du locataire source Azure AD et ne sont pas migrées au locataire Azure AD cible. Vous devez recréer vos attributions de rôle dans le locataire cible.
- Si vous êtes un compte Azure AD administrateur général et vous n’avez pas accès à un abonnement après qu’il a été déplacé entre les locataires, utilisez le **Access management pour les ressources Azure** activer/désactiver temporairement [élever votre accès](elevate-access-global-admin.md) pour accéder à l’abonnement.

## <a name="issues-with-service-admins-or-co-admins"></a>Problèmes liés aux coadministrateurs ou administrateurs de service

- Si vous rencontrez des problèmes avec l’administrateur de Service ou aux coadministrateurs, consultez [administrateurs d’abonnements Azure ajouter ou modifier](../billing/billing-add-change-azure-subscription-administrator.md) et [rôles d’administrateur abonnement classique, les rôles RBAC Azure et Azure AD rôles d’administrateur](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Accès refusé ou des erreurs d’autorisation

- Si vous obtenez l’erreur d’autorisations « le client avec l’id d’objet n’est pas autorisé à effectuer l’action sur l’étendue (code : AuthorizationFailed) » lorsque vous essayez de créer une ressource, vérifiez que vous êtes actuellement connecté avec un utilisateur se voit attribuer un rôle qui a l’autorisation d’écriture à la ressource à l’étendue sélectionnée. Par exemple, pour gérer les machines virtuelles dans un groupe de ressources, vous devez avoir le [collaborateur de Machine virtuelle](built-in-roles.md#virtual-machine-contributor) rôle sur le groupe de ressources (ou l’étendue parente). Pour obtenir la liste des autorisations pour chaque rôle intégré, consultez [rôles intégrés pour les ressources Azure](built-in-roles.md).
- Si vous obtenez l’erreur d’autorisations « Vous n’êtes pas autorisé à créer une demande de support » lorsque vous tentez de créer ou mettre à jour un ticket de support, vérifiez que vous êtes actuellement connecté avec un utilisateur se voit attribuer un rôle qui a le `Microsoft.Support/supportTickets/write` autorisation, tels que [Prend en charge de contributeur de demande](built-in-roles.md#support-request-contributor).

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

Certaines fonctionnalités de [Azure Functions](../azure-functions/functions-overview.md) nécessitent un accès en écriture. Par exemple, si un utilisateur est assigné au rôle de Lecteur, il ne peut pas voir les fonctions au sein d’une application de fonction. Le portail affiche **(aucun accès)**.

![Aucun accès aux applications de fonction](./media/troubleshooting/functionapps-noaccess.png)

Un lecteur peut cliquer sur l’onglet **Fonctionnalités de plateforme**, puis cliquez sur **Tous les paramètres** pour afficher certains paramètres liés à une application de fonction (semblable à une application Web), mais il ne peut pas modifier ces paramètres.

## <a name="next-steps"></a>Étapes suivantes
* [Gérer l’accès aux ressources Azure à l’aide du contrôle RBAC et du portail Azure](role-assignments-portal.md)
* [Afficher les journaux d’activité des changements de contrôle d’accès en fonction du rôle pour les ressources Azure](change-history-report.md)

