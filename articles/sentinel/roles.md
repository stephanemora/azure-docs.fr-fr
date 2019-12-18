---
title: Autorisations dans Azure Sentinel | Microsoft Docs
description: Cet article explique comment Azure Sentinel utilise le contrôle d’accès en fonction du rôle pour attribuer des autorisations à des utilisateurs et identifie les actions autorisées pour chaque rôle.
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: rkarlin
ms.openlocfilehash: 0479eba13fd747ea1bcab2672fce1df2156802e8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947584"
---
# <a name="permissions-in-azure-sentinel"></a>Autorisations dans Azure Sentinel

Azure Sentinel utilise le  [contrôle d’accès en fonction du rôle (RBAC)](../role-based-access-control/role-assignments-portal.md) pour fournir des  [rôles intégrés](../role-based-access-control/built-in-roles.md) susceptibles d’être attribués à des utilisateurs, des groupes et des services dans Azure.

L’utilisation de RBAC vous permet d’utiliser et de créer des rôles au sein de votre équipe en charge des opérations de sécurité pour accorder l’accès approprié à Azure Sentinel. En fonction des rôles, vous bénéficiez d’un contrôle précis sur ce que les utilisateurs disposant d’un accès à Azure Sentinel peuvent voir. Vous pouvez attribuer des rôles RBAC directement dans l’espace de travail Azure Sentinel, ou bien à un abonnement ou à un groupe de ressources auquel appartient l’espace de travail.

Il existe trois rôles Azure Sentinel intégrés spécifiques.  
**Tous les rôles intégrés Azure Sentinel accordent un accès en lecture aux données de votre espace de travail Azure Sentinel.**
- [Lecteur Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Répondeur Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Contributeur Azure Sentinel](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Outre les rôles RBAC dédiés à Azure Sentinel, il existe des rôles RBAC Azure et Log Analytics qui peuvent accorder un ensemble plus large d’autorisations qui incluent l’accès à votre espace de travail Azure Sentinel et à d’autres ressources :

- **Rôles Azure :** [Propriétaire](../role-based-access-control/built-in-roles.md#owner), [Contributeur](../role-based-access-control/built-in-roles.md#contributor) et [Lecteur](../role-based-access-control/built-in-roles.md#reader). Les rôles Azure accordent l’accès à l’ensemble de vos ressources Azure, notamment aux espaces de travail Log Analytics et aux ressources Azure Sentinel.

-   **Rôles Log Analytics :** [Contributeur Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-contributor), [Lecteur Log Analytics](../role-based-access-control/built-in-roles.md#log-analytics-reader). Les rôles Log Analytics accordent l’accès à l’ensemble de vos espaces de travail Log Analytics. 

> [!NOTE]
> Les rôles Log Analytics accordent également l’accès en lecture à toutes les ressources Azure, mais n’attribuent des autorisations en écriture qu’aux ressources Log Analytics.


Par exemple, un utilisateur à qui les rôles **Lecteur Azure Sentinel** et **Contributeur Azure** (mais pas **Contributeur Azure Sentinel**) ont été attribués peut modifier des données dans Azure Sentinel, même s’il dispose uniquement d’autorisations **Lecteur Azure Sentinel**. Par conséquent, si vous voulez accorder des autorisations à un utilisateur uniquement dans Azure Sentinel, vous devez supprimer soigneusement les autorisations préalables de cet utilisateur, en veillant à ne pas annuler un rôle d’autorisation nécessaire pour une autre ressource.

> [!NOTE]
>- Azure Sentinel utilise des playbooks pour la réponse automatisée face aux menaces. Les playbooks exploitent Azure Logic Apps et sont une ressource Azure distincte. Vous pouvez attribuer à des membres spécifiques de votre équipe en charge des opérations de sécurité la possibilité d’utiliser Logic Apps pour les opérations d’orchestration de la sécurité, d’automatisation de la sécurité et de réponse (SOAR) aux problèmes de sécurité. Vous pouvez utiliser le rôle [Contributeur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-contributor) ou le rôle [Opérateur d’application logique](../role-based-access-control/built-in-roles.md#logic-app-operator) pour attribuer une autorisation explicite pour l’utilisation des playbooks.
>- Pour ajouter des connecteurs de données, les rôles nécessaires pour chaque connecteur sont par type de connecteur et sont listés dans la page de connecteur appropriée. De plus, pour pouvoir connecter toute source de données, vous devez disposer d’une autorisation en écriture sur l’espace de travail Azure Sentinel.



## <a name="roles-and-allowed-actions"></a>Rôles et actions autorisées

Le tableau suivant affiche les rôles et les actions autorisées dans Azure Sentinel. Un X indique que l’action est autorisée pour ce rôle.

| Role | Créer et exécuter des playbooks| Créer et modifier des tableaux de bord, des règles d’analytique et d’autres ressources Azure Sentinel | Gérer les incidents (ignorer, attribuer, etc.) | Afficher des données, des incidents, des tableaux de bord et d’autres ressources Azure Sentinel |
|--- |---|---|---|---|
| Lecteur Azure Sentinel | -- | -- | -- | X |
| Répondeur Azure Sentinel|--|--| X | X |
| Contributeur Azure Sentinel | -- | X | X | X |
| Contributeur Azure Sentinel + contributeur d’application logique | X | X | X | X |


> [!NOTE]
> - Nous vous recommandons d’attribuer le rôle le moins permissif permettant aux utilisateurs d’effectuer leurs tâches. Par exemple, attribuez le rôle Contributeur Azure Sentinel uniquement aux utilisateurs qui doivent créer des règles ou des tableaux de bord.
> - Nous vous recommandons de définir des autorisations pour Azure Sentinel dans l’étendue du groupe de ressources, afin que l’utilisateur puisse avoir accès à tous les espaces de travail Azure Sentinel du même groupe de ressources.
>
## <a name="building-custom-rbac-roles"></a>Génération de rôles RBAC personnalisés

En plus d’utiliser des rôles RBAC intégrés, ou au lieu d’en utiliser, vous pouvez créer des rôles RBAC personnalisés pour Azure Sentinel. Les rôles RBAC personnalisés pour Azure Sentinel sont créés de la même façon que les autres rôles [RBAC Azure personnalisés](../role-based-access-control/custom-roles-rest.md#create-a-custom-role), c’est-à-dire selon des [autorisations pour les ressources Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) et pour les [ressources Azure Log Analytics](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Contrôle d’accès en fonction du rôle (RBAC) avancé sur les données que vous stockez dans Azure Sentinel
  
Vous pouvez utiliser le contrôle d’accès en fonction du rôle avancé Log Analytics sur les données de votre espace de travail Azure Sentinel. Cela comprend à la fois le contrôle d’accès en fonction du rôle par type de données et le contrôle d’accès en fonction du rôle centré sur la ressource. Pour plus d’informations sur les rôles Log Analytics, consultez  [Gérer les données du journal et les espaces de travail dans Azure Monitor](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions).

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez appris à utiliser des rôles pour les utilisateurs Azure Sentinel et découvert ce que chaque rôle permet aux utilisateurs de faire.

* [Blog Azure Sentinel](https://aka.ms/azuresentinelblog) : accédez à des billets de blog sur la sécurité et la conformité Azure.
