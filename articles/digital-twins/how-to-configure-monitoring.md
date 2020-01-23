---
title: Guide pratique pour configurer la supervision - Azure Digital Twins | Microsoft Docs
description: Découvrez comment configurer les options de monitorage et de journalisation pour Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: 38f5af3cb2006e4b029f8979a213c0af10f1ed8a
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863578"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Guide pratique pour configurer la supervision dans Azure Digital Twins

Azure Digital Twins prend en charge de solides fonctionnalités de journalisation, de supervision et d’analytique. Les développeurs de solutions peuvent utiliser les journaux Azure Monitor, les journaux de diagnostic, les journaux d’activité et d’autres services pour prendre en charge les besoins de supervision complexes d’une application IoT. Vous pouvez combiner les options de journalisation afin d’interroger ou d’afficher des enregistrements entre plusieurs services et de fournir une couverture de journalisation précise pour de nombreux services.

Cet article récapitule les options de journalisation et de supervision, puis explique comment les combiner spécifiquement pour Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="review-activity-logs"></a>Examiner les journaux d’activité

Les [journaux d’activité](../azure-monitor/platform/platform-logs-overview.md) Azure fournissent des insights rapides sur les historiques des événements et des opérations de niveau abonnement pour chaque instance du service Azure.

Les événements de niveau abonnement sont les suivants :

* Création de ressources
* Suppression de ressources
* Création de secrets d’application
* Intégration à d’autres services

La journalisation de l’activité pour Azure Digital Twins est activée par défaut et accessible depuis le portail Azure en :

1. Sélectionnant votre instance Azure Digital Twins.
1. Choisissant **Journal d’activité** pour afficher le panneau d’affichage :

    [![Journal d’activité](media/how-to-configure-monitoring/activity-log.png)](media/how-to-configure-monitoring/activity-log.png#lightbox)

Pour la journalisation de l’activité avancée :

1. Sélectionnez l’option **Journaux d’activité** pour afficher la **Vue d’ensemble d’Activity Log Analytics** :

    [![Sélection](media/how-to-configure-monitoring/activity-log-select.png)](media/how-to-configure-monitoring/activity-log-select.png#lightbox)

1. La **Vue d’ensemble de l’analytique des journaux d’activité** récapitule les principales données du journal d’activité :

    [![Vue d’ensemble de l’activité Log Analytics]( media/how-to-configure-monitoring/log-analytics-overview.png)]( media/how-to-configure-monitoring/log-analytics-overview.png#lightbox)

>[!TIP]
>Utilisez **Journaux d’activité** pour obtenir des insights rapides sur les événements de niveau abonnement.

## <a name="enable-customer-diagnostic-logs"></a>Activer les journaux de diagnostic des clients

Vous pouvez définir des [paramètres de diagnostic](../azure-monitor/platform/platform-logs-overview.md) Azure pour chaque instance Azure afin de compléter la journalisation de l’activité. Bien que les journaux d’activité se rapportent aux événements de niveau abonnement, la journalisation des diagnostics fournit des insights sur l’historique opérationnel des ressources elles-mêmes.

Voici quelques exemples de journalisation des diagnostics :

* Heure d’exécution d’une fonction définie par l’utilisateur
* Code d’état de réponse d’une demande d’API réussie
* Récupération d’une clé d’application à partir d’un coffre

Pour activer les journaux de diagnostic pour une instance :

1. Affichez la ressource dans le portail Azure.
1. Sélectionnez **Paramètres de diagnostic** :

    [![Paramètres de diagnostic (1)](media/how-to-configure-monitoring/diagnostic-settings-one.png)](media/how-to-configure-monitoring/diagnostic-settings-one.png#lightbox)

1. Sélectionnez **Activer les diagnostics** pour collecter des données (s’ils n’étaient pas activés).
1. Renseignez les champs demandés et sélectionnez comment et où les données doivent être enregistrées :

    [![Paramètres de diagnostic (2)](media/how-to-configure-monitoring/diagnostic-settings-two.png)](media/how-to-configure-monitoring/diagnostic-settings-two.png#lightbox)

    Les journaux de diagnostic sont souvent enregistrés à l’aide de [Stockage Fichier Azure](../storage/files/storage-files-deployment-guide.md) et partagés avec les [journaux Azure Monitor](../azure-monitor/log-query/get-started-portal.md). Les deux options peuvent être sélectionnées.

>[!TIP]
>Utilisez des **journaux de diagnostic** pour connaître les opérations de ressource.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor et Log Analytics

Les applications IoT unifient des ressources, des appareils, des emplacements et des données disparates. La journalisation affinée fournit des informations détaillées sur chaque élément, service ou composant de l’architecture globale de l’application, mais une vue d’ensemble unifiée est souvent nécessaire pour la maintenance et le débogage.

Azure Monitor inclut le puissant service Log Analytics, qui permet de voir et d’analyser les sources de journalisation dans un seul emplacement. Azure Monitor est donc très utile pour l’analyse des journaux d’activité dans des applications IoT sophistiquées.

Voici quelques exemples d’utilisation :

* Interrogation de plusieurs historiques de journal de diagnostic
* Visualisation des journaux d’activité relatifs à plusieurs fonctions définies par l’utilisateur
* Affichage des journaux d’activité relatifs à deux ou plusieurs services dans un intervalle de temps spécifique

Les [journaux Azure Monitor](../azure-monitor/log-query/log-query-overview.md) prennent en charge l’interrogation complète des journaux. Pour configurer ces fonctionnalités puissantes :

1. Recherchez **Log Analytics** dans le portail Azure.
1. Les instances de votre **espace de travail Log Analytics** disponibles apparaissent. Choisissez-en une et sélectionnez les **Journaux d’activité** à interroger :

    [![Log analytics](media/how-to-configure-monitoring/log-analytics.png)](media/how-to-configure-monitoring/log-analytics.png#lightbox)

1. Si vous n’avez pas d’instance d’**espace de travail Log Analytics**, vous pouvez créer un espace de travail en cliquant sur le bouton **Ajouter** :

    [![Créer un espace de travail OMS](media/how-to-configure-monitoring/log-analytics-oms.png)](media/how-to-configure-monitoring/log-analytics-oms.png#lightbox)

Une fois votre instance d’**espace de travail Log Analytics** provisionnée, vous pouvez utiliser des requêtes puissantes pour rechercher des entrées dans plusieurs journaux ou effectuer une recherche à l’aide de critères spécifiques au moyen de la **Gestion des journaux d’activité** :

   [![Gestion du journal](media/how-to-configure-monitoring/log-analytics-management.png)](media/how-to-configure-monitoring/log-analytics-management.png#lightbox)

Pour plus d’informations sur les opérations de requêtes puissantes, consultez [Prise en main des requêtes](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Le premier envoi d’événements à l’**espace de travail Log Analytics** peut prendre 5 minutes.

Mes journaux Azure Monitor fournissent également des services de notification d’erreur et d’alerte puissants, que vous pouvez voir en sélectionnant **Diagnostiquer et résoudre les problèmes** :

   [![Notifications d’alerte et d’erreur](media/how-to-configure-monitoring/log-analytics-notifications.png)](media/how-to-configure-monitoring/log-analytics-notifications.png#lightbox)

>[!TIP]
>Utilisez l’**espace de travail Log Analytics** pour interroger les historiques de journal de plusieurs services, abonnements ou fonctionnalités d’application.

## <a name="other-options"></a>Autres options

Azure Digital Twins prend également en charge la journalisation et l’audit de sécurité spécifiques à une application. Pour une présentation complète de toutes les options de journalisation Azure disponibles pour votre instance Azure Digital Twins, lisez l’article [Journalisation et audit Azure](../security/fundamentals/log-audit.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur les [journaux d’activité](../azure-monitor/platform/platform-logs-overview.md) Azure.

- Approfondissez votre connaissance des paramètres de diagnostic Azure en lisant une [présentation des journaux de diagnostic](../azure-monitor/platform/platform-logs-overview.md).

- En savoir plus sur les [journaux Azure Monitor](../azure-monitor/log-query/get-started-portal.md).
