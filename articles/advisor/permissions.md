---
title: Autorisations dans Azure Advisor
description: L’Assistant autorisations et comment ils peuvent bloquer votre capacité à configurer des abonnements ou reporter ou ignorer les recommandations.
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467609"
---
# <a name="permissions-in-azure-advisor"></a>Autorisations dans Azure Advisor

Azure Advisor fournit des recommandations basées sur l’utilisation et la configuration de vos ressources Azure et les abonnements. L’Assistant utilise le [rôles intégrés](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) fourni par [contrôle d’accès en fonction du rôle](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) pour gérer votre accès aux recommandations et des fonctionnalités de l’Assistant. 

## <a name="roles-and-their-access"></a>Rôles et leur accès

Le tableau suivant définit les rôles et l’accès qu'au sein de l’Assistant :

| **Rôle** | **Afficher les recommandations** | **Modifier les règles** | **Modifier la configuration de l’abonnement** | **Modifier la configuration du groupe de ressources**| **Ignorer et différer des recommandations**|
|---|:---:|:---:|:---:|:---:|:---:|
|Propriétaire de l’abonnement|**X**|**X**|**X**|**X**|**X**|
|Collaborateur de l’abonnement|**X**|**X**|**X**|**X**|**X**|
|Lecteur de l’abonnement|**X**|--|--|--|--|
|Groupe de ressources propriétaire|**X**|--|--|**X**|**X**|
|Groupe de ressources contributeur|**X**|--|--|**X**|**X**|
|Groupe de ressources lecteur|**X**|--|--|--|--|
|Propriétaire de la ressource|**X**|--|--|--|**X**|
|Collaborateur de ressource|**X**|--|--|--|**X**|
|Lecteur de ressources|**X**|--|--|--|--|

> [!NOTE]
> Accès pour afficher les recommandations est dépendante de l’accès à la ressource affectée de la recommandation.

## <a name="permissions-and-unavailable-actions"></a>Autorisations et les actions non disponibles

Un manque d’autorisations appropriées peut bloquer votre capacité à effectuer des actions dans l’Assistant. Voici certains problèmes courants.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Impossible de configurer les abonnements ou groupes de ressources

Lorsque vous tentez de configurer des abonnements ou groupes de ressources dans l’Assistant, vous pouvez voir que l’option pour inclure ou exclure est désactivée. Cet état indique que vous n’avez pas d’un niveau suffisant d’autorisation pour ce groupe de ressources ou d’un abonnement. Pour résoudre ce problème, découvrez comment [accorder un accès utilisateur](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Impossible de reporter ou ignorer une recommandation

Si vous recevez une erreur lorsque vous tentez de reporter ou ignorer une recommandation, vous ne pouvez pas avoir des autorisations suffisantes. Assurez-vous que vous avez au moins un accès collaborateur à la ressource affectée de la recommandation vous différer ou ignorer. Pour résoudre ce problème, découvrez comment [accorder un accès utilisateur](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal).

## <a name="next-steps"></a>Étapes suivantes

Cet article a fourni une vue d’ensemble de comment l’Assistant utilise RBAC pour contrôler les autorisations utilisateur et comment résoudre les problèmes courants. Pour en savoir plus sur Advisor, consultez les ressources suivantes :

- [Présentation d’Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Bien démarrer avec Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-get-started)
