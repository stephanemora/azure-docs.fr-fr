---
title: Voir l’activité des fournisseurs de services
description: Les clients peuvent consulter l’activité journalisée pour voir les actions effectuées par les fournisseurs de services par le biais de la gestion des ressources déléguées Azure.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: a6ae5668601e3587b7306ba1652f6b107800fcb3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75527822"
---
# <a name="view-service-provider-activity"></a>Voir l’activité des fournisseurs de services

Les clients qui ont délégué des abonnements pour la gestion des ressources déléguées Azure peuvent [consulter les données du journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md) pour voir toutes les actions effectuées. Les clients bénéficient d’une visibilité complète sur les opérations effectuées par les fournisseurs de services par le biais de la gestion des ressources déléguées Azure, ainsi que sur celles effectuées par les utilisateurs dans le propre locataire Azure Active Directory (Azure AD) du client.

## <a name="view-activity-log-data"></a>Voir les données du journal d’activité

Vous pouvez [voir le journal d’activité](../../azure-monitor/platform/activity-log-view.md) à partir du menu **Superviser** dans le portail Azure. Pour limiter les résultats à un abonnement spécifique, vous pouvez utiliser les filtres afin de sélectionner un abonnement spécifique. Vous pouvez également [voir et récupérer les événements du journal d’activité](../../azure-monitor/platform/activity-log-view.md) programmatiquement.

> [!NOTE]
> Les utilisateurs du locataire d’un fournisseur de services peuvent voir les résultats du journal d’activité d’un abonnement délégué dans un locataire client s’ils ont reçu le rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) (ou un autre rôle intégré qui comprend un accès Lecteur) quand l’abonnement a été intégré dans la gestion des ressources déléguées Azure.

Dans le journal d’activité, vous verrez le nom de l’opération et son état, ainsi que la date et l’heure de son exécution. La colonne **Événement lancé par** indique l’utilisateur qui a effectué l’opération, qu’il s’agisse d’un utilisateur dans le locataire d’un fournisseur de services agissant par le biais de la gestion des ressources déléguées Azure ou d’un utilisateur dans le propre locataire du client. Notez que le nom de l’utilisateur est indiqué, et non le locataire ou le rôle auquel l’utilisateur a été affecté pour cet abonnement.

L’activité journalisée des 90 derniers jours est disponible dans le portail Azure. Pour savoir comment stocker ces données pendant plus de 90 jours, consultez [Collecter et analyser les journaux d’activité Azure dans l’espace de travail Log Analytics dans Azure Monitor](../../azure-monitor/platform/activity-log-collect.md).

## <a name="set-alerts-for-critical-operations"></a>Définir des alertes pour les opérations critiques

Pour rester informé des opérations critiques que les fournisseurs de services (ou les utilisateurs de votre propre locataire) effectuent, nous vous recommandons de créer des [alertes de journal d’activité](../../azure-monitor/platform/activity-log-alerts.md). Par exemple, vous souhaiterez peut-être effectuer le suivi de toutes les actions d’administration pour un abonnement ou recevoir une notification quand une machine virtuelle d’un groupe de ressources particulier est supprimée. Quand vous créez des alertes, celles-ci incluent les actions effectuées par les utilisateurs dans le propre locataire du client, ainsi que dans tous les locataires de gestion.

Pour plus d’informations, consultez [Créer et gérer les alertes de journal d’activité](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Créer des requêtes de journal

Vous pouvez créer des requêtes pour analyser votre activité journalisée ou vous concentrer sur des éléments spécifiques. Par exemple, il est possible qu’un audit vous oblige à signaler toutes les actions de niveau administratif effectuées sur un abonnement. Vous pouvez créer une requête pour filtrer uniquement ces actions et trier les résultats selon l’utilisateur, la date ou une autre valeur.

Pour plus d’informations, consultez [Vue d’ensemble des requêtes de journal dans Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Monitor](../../azure-monitor/index.yml).
- Découvrez comment [afficher et gérer les offres du fournisseur de services](view-manage-service-providers.md) dans le portail Azure.