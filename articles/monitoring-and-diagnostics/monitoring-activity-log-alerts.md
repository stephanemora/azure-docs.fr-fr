---
title: Alertes de journal d’activité dans Azure Monitor
description: Recevez des notifications par SMS, webhook, e-mail, etc. lors de la survenue de certains événements dans le journal d’activité.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5ddf510d50f38ed9aaf742bd06c330e53ffe1391
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992794"
---
# <a name="alerts-on-activity-log"></a>Alertes sur le journal d’activité 

## <a name="overview"></a>Vue d’ensemble
Les alertes de journal d’activité s’activent lorsqu’un nouvel événement du journal d’activité correspond aux conditions spécifiées dans l’alerte. Il s’agit de ressources Azure, et peuvent donc être créées à l’aide d’un modèle Azure Resource Manager. Elles peuvent également être créées, mises à jour ou supprimées dans le portail Azure. Cet article présente les concepts qui sous-tendent les alertes de journal d’activité. Cet article décrit comment utiliser le portail Azure pour configurer une alerte sur les événements de journal d’activité. Pour plus d’informations sur son utilisation, consultez [Créer et gérer les alertes de journal d’activité](alert-activity-log.md).

> [!NOTE]
> Les alertes **ne peuvent pas** être créées pour des événements dans la catégorie d’alerte du journal d’activité

En général, vous créez des alertes du journal d’activité pour recevoir des notifications lorsque :

* des fonctionnements particuliers se produisent dans votre abonnement Azure, souvent étendus à des groupes de ressources ou à des ressources spécifiques. Par exemple, vous pouvez désirer être averti lorsqu’une machine virtuelle dans myProductionResourceGroup est supprimée. Vous pouvez également vouloir être averti si de nouveaux rôles sont attribués à un utilisateur dans votre abonnement.
* Un événement d’intégrité du service se produit. Les événements d’intégrité du service incluent la notification des incidents et des événements de maintenance qui s’appliquent aux ressources de votre abonnement.

Pour bien comprendre les conditions dans lesquelles des règles d’alerte peuvent être créées sur le journal d’activité, il est possible d’explorer ou de filtrer les événements par le biais du [Journal d’activité dans le portail Azure](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). Dans Azure Monitor - Journal d’activité, il est possible de filtrer ou de rechercher l’événement requis, puis de créer une alerte à l’aide du bouton **Ajouter une alerte de journal d’activité**.

Dans les deux cas, l’alerte du journal d’activité surveille uniquement les événements de l’abonnement dans lequel elle a été créée.

Vous pouvez configurer une alerte de journal d’activité en fonction de n’importe quelle propriété de niveau supérieur de l’objet JSON d’un événement de journal d’activité. Pour plus d’informations, reportez-vous à la rubrique [Présentation du journal d’activité Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Pour en savoir plus sur les événements d’intégrité du service, consultez [Recevoir des alertes de journal d’activité sur les notifications de service](./monitoring-activity-log-alerts-on-service-notifications.md). 

Les alertes de journal d’activité ont quelques options communes :

- **Catégorie** : administration, intégrité du service, mise à l’échelle automatique, sécurité, stratégie et recommandation. 
- **Étendue** : ressource individuelle ou ensemble de ressources pour qui l’alerte sur le journal d’activité est définie. L’étendue d’une alerte de journal d’activité peut être définie à différents niveaux :
    - Au niveau de la ressource : par exemple, pour une machine virtuelle spécifique
    - Au niveau du groupe de ressources : par exemple, toutes les machines virtuelles d’un groupe de ressources spécifique
    - Au niveau de l’abonnement : par exemple, toutes les machines virtuelles d’un abonnement (ou) toutes les ressources d’un abonnement
- **Groupe de ressources** : par défaut, la règle d’alerte est enregistrée dans le même groupe de ressources que celui de la cible définie dans l’étendue. L’utilisateur peut également définir le groupe de ressources où la règle d’alerte doit être stockée.
- **Type de ressource** : le Gestionnaire des ressources a défini l’espace de noms pour la cible de l’alerte.

- **Nom de l’opération** : le nom de l’opération de Contrôle d’accès basé sur le rôle de Gestionnaire des ressources.
- **Niveau** : le niveau de gravité de l’événement (détaillé, informationnel, avertissement, erreur ou critique).
- **État** : l’état de l’événement, généralement « Démarré », « Échoué » ou « Réussi ».
- **Événement lancé par** : également appelé l’« appelant ». L’adresse e-mail ou l’identificateur Azure Active Directory de l’utilisateur qui a effectué l’opération.

> [!NOTE]
> Dans un abonnement allant jusqu’à 100 règles d’alerte pour l’activité d’étendue dans une seule ressource, dans toutes les ressources d’un groupe de ressources (ou) au niveau de l’abonnement tout entier.

Lorsqu’une alerte du journal d’activité devient active, elle utilise un groupe d’actions pour générer des actions ou des notifications. Un groupe d’actions est un jeu réutilisable de destinataires de notifications, telles que des adresses de messagerie, des URL webhook ou des numéros de téléphone SMS. Les destinataires peuvent être référencés à partir de plusieurs alertes pour centraliser et regrouper vos canaux de notification. Lorsque vous définissez votre alerte de journal d’activité, vous avez deux options. Vous pouvez :

* utiliser un groupe d’actions existant dans votre alerte de journal d’activité.
* créer un nouveau groupe d’action.

Pour en savoir plus sur les groupes d’actions, consultez [Créer et gérer des groupes d’actions dans le portail Azure](monitoring-action-groups.md).


## <a name="next-steps"></a>Étapes suivantes
- Obtenir une [vue d’ensemble des alertes](monitoring-overview-alerts.md).
- En savoir plus sur la [création et la modification des alertes de journal d’activité](alert-activity-log.md).
- Consultez le [schéma webhook des alertes de journal d’activité](monitoring-activity-log-alerts-webhook.md).
- En savoir plus sur les [notifications sur l’intégrité du service](monitoring-service-notifications.md).

