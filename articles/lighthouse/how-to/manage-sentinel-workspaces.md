---
title: Gérer les espaces de travail Azure Sentinel à grande échelle
description: Apprenez à gérer efficacement Azure Sentinel sur les ressources client déléguées.
ms.date: 06/17/2020
ms.topic: how-to
ms.openlocfilehash: fc6d66a31ed46766bbe664fd8656792c97ff69fd
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163235"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Gérer les espaces de travail Azure Sentinel à grande échelle

En tant que fournisseur de services, vous avez peut-être intégré les locataires de plusieurs clients à [Azure Lighthouse](../overview.md). Azure Lighthouse permet aux fournisseurs de services d’effectuer des opérations à grande échelle sur plusieurs locataires Azure Active Directory (Azure AD) à la fois, améliorant ainsi l’efficacité des tâches de gestion.

Azure Sentinel assure une analyse de sécurité intelligente et fournit des informations sur les menaces. Elle constitue une solution unique pour la détection des alertes, la visibilité des menaces, la chasse proactive et la réponse face aux menaces. Avec Azure Lighthouse, vous pouvez gérer plusieurs espaces de travail Azure Sentinel entre les locataires à grande échelle. Cela permet des scénarios tels que l’exécution de requêtes sur plusieurs espaces de travail ou la création de classeurs pour visualiser et surveiller les données de vos sources de données connectées afin de mieux les exploiter. Les protocoles Internet tels que les requêtes et les guides opérationnels restent dans votre locataire de gestion, mais ils peuvent être utilisés pour effectuer la gestion de la sécurité dans les locataires clients.

Cette rubrique fournit une vue d’ensemble de l’utilisation d’[Azure Sentinel](../../sentinel/overview.md) de manière évolutive pour la visibilité entre locataires et les services de sécurité managés.

> [!TIP]
> Bien que nous faisons référence aux fournisseurs de services et aux clients dans cette rubrique, ces instructions s’appliquent également aux [entreprises utilisant Azure Lighthouse pour gérer plusieurs locataires](../concepts/enterprise.md).

## <a name="architectural-considerations"></a>Considérations sur l’architecture

Pour un fournisseur MSSP (Managed Security Service Provider) souhaitant constituer une offre de sécurité en tant que service avec Azure Sentinel, un centre d’opérations de sécurité (SOC) peut être nécessaire pour surveiller, gérer et configurer de manière centralisée plusieurs espaces de travail Azure Sentinel déployés au sein de locataires clients individuels. De même, les entreprises avec plusieurs locataires Azure AD peuvent souhaiter gérer de manière centralisée plusieurs espaces de travail Azure Sentinel déployés chez l’ensemble de leurs locataires.

Ce modèle centralisé de déploiement présente les avantages suivants :

- La propriété des données est conservée par chaque locataire géré.
- Il prend en charge des exigences de stockage des données dans les limites géographiques.
- Il garantit l’isolation des données, car les données de plusieurs clients ne sont pas stockées dans le même espace de travail. 
- Il empêche l’exfiltration des données des locataires managés, garantissant ainsi la conformité des données.
- Les coûts associés sont facturés à chaque locataire géré, plutôt qu’au locataire gérant.
- Les données de toutes les sources de données et de tous les connecteurs de données intégrés à Azure Sentinel (comme les journaux d’activité Azure AD, les journaux Office 365 ou les alertes de Protection Microsoft contre les menaces) sont conservées dans chaque locataire client.
- Il réduit le temps de réponse du réseau.
- Il est facile d’ajouter ou de supprimer de nouvelles filiales ou clients.

## <a name="granular-role-based-access-control-rbac"></a>Contrôle d’accès granulaire en fonction du rôle (RBAC)

Chaque abonnement client géré par un MSSP doit être [intégré à Azure Lighthouse](onboard-customer.md). Cela permet aux utilisateurs désignés du locataire gérant d’accéder aux opérations de gestion et de les effectuer sur les espaces de travail Azure Sentinel déployés chez les locataires clients.

Lorsque vous créez vos autorisations, vous pouvez affecter les rôles intégrés Azure Sentinel aux utilisateurs, groupes ou principaux du service dans votre locataire gérant :

- [Lecteur Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Répondeur Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Contributeur Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Vous pouvez également affecter des rôles intégrés supplémentaires pour exécuter des fonctions additionnelles. Pour plus d’informations sur les rôles spécifiques qui peuvent être utilisés avec Azure Sentinel, consultez les [Autorisations dans Azure Sentinel](../../sentinel/roles.md).

Une fois que vous avez intégré vos clients, les utilisateurs désignés peuvent se connecter à votre locataire gérant et [accéder directement à l’espace de travail Azure Sentinel du client](../../sentinel/multiple-tenants-service-providers.md) avec les rôles qui ont été affectés.

## <a name="view-and-manage-incidents-across-workspaces"></a>Afficher et gérer les incidents des espaces de travail

Si vous gérez des ressources Azure Sentinel pour plusieurs clients, vous pouvez afficher et gérer les incidents dans plusieurs espaces de travail et plusieurs locataires à la fois. Pour plus d’informations, consultez [Travailler avec des incidents dans plusieurs espaces de travail à la fois](../../sentinel/multiple-workspace-view.md) et [Étendre Azure Sentinel sur les espaces de travail et les locataires](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Assurez-vous que les utilisateurs de votre locataire gérant disposent d’autorisations en lecture et en écriture sur tous les espaces de travail qui sont managés. Si un utilisateur dispose uniquement d’autorisations de lecture sur certains espaces de travail, des messages d’avertissement peuvent s’afficher lorsqu’il sélectionne des incidents dans ces espaces de travail et il ne peut pas modifier ces incidents, ni aucun autre sélectionné (même si vous disposez des autorisations pour les autres).

## <a name="configure-playbooks-for-mitigation"></a>Configurer des playbooks d’atténuation

[Les playbooks](../../sentinel/tutorial-respond-threats-playbook.md) peuvent être utilisés pour atténuer automatiquement lorsqu’une alerte est déclenchée. Ces playbooks peuvent être exécutés manuellement ou s’exécuter automatiquement lorsque des alertes spécifiques sont déclenchées. Les playbooks peuvent être déployés dans le locataire gérant ou client, avec les procédures de réponse configurées en fonction des utilisateurs du locataire qui agir pour répondre à une menace de sécurité.

## <a name="create-cross-tenant-workbooks"></a>Créer des classeurs inter-locataires

[Les classeurs Azure Monitor dans Azure Sentinel](../../sentinel/overview.md#workbooks) vous aident à visualiser et à surveiller les données de vos sources de données connectées pour obtenir des Insights. Vous pouvez utiliser les modèles de classeurs intégrés dans Azure Sentinel ou créer des classeurs personnalisés pour vos scénarios.

Vous pouvez déployer des classeurs dans votre client gérant et créer des tableaux de bord à l’échelle pour surveiller et interroger les données des locataires clients. Pour plus d’informations, consultez l’article [Surveiller plusieurs espaces de travail](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). Notez que certaines fonctionnalités ne sont [pas prises en charge sur plusieurs espaces de travail](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

Vous pouvez également déployer des classeurs directement dans un locataire individuel que vous gérez pour des scénarios qui lui sont spécifiques.

## <a name="run-queries-across-azure-sentinel-workspaces"></a>Exécuter des requêtes dans des espaces de travail Azure Sentinel

Vous pouvez créer et enregistrer des requêtes d’analytique des journaux d’activité pour la détection des menaces de manière centralisée dans le locataire gérant. Ces requêtes peuvent ensuite être exécutées sur l’ensemble des espaces de travail Azure Sentinel de vos clients à l’aide de l’opérateur Union et de l’expression de langage d’espace de travail (). Pour plus d’informations, consultez l’article [Interroger plusieurs espaces de travail](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Utiliser l’automatisation pour la gestion inter-espaces de travail

Vous pouvez utiliser l’automatisation pour gérer plusieurs espaces de travail Azure Sentinel et configurer des [requêtes de chasse](../../sentinel/hunting.md), des playbooks et des classeurs. Pour plus d’informations, consultez l’article [Gestion inter-espaces de travail à l’aide de l’automatisation](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

Notez que certaines fonctionnalités ne sont [actuellement pas prises en charge sur plusieurs espaces de travail](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Sentinel](../../sentinel/overview.md).
- Accédez à la page de tarification [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Découvrez les [Expériences de gestion inter-locataire](../concepts/cross-tenant-management-experience.md).

