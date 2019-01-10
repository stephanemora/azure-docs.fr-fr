---
title: Guide pratique pour configurer la supervision dans Azure Digital Twins | Microsoft Docs
description: Guide pratique pour configurer la supervision dans Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: 2749a5c6c4e6003c51523d83c46b48d3b55b3d45
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53807582"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Guide pratique pour configurer la supervision dans Azure Digital Twins

Azure Digital Twins prend en charge de solides fonctionnalités de journalisation, de supervision et d’analytique. Les développeurs de solutions peuvent utiliser Azure Log Analytics, les journaux de diagnostic, la journalisation de l’activité et d’autres services pour prendre en charge les besoins de supervision complexes d’une application IoT. Vous pouvez combiner les options de journalisation afin d’interroger ou d’afficher des enregistrements entre plusieurs services et de fournir une couverture de journalisation précise pour de nombreux services.

Cet article récapitule les options de journalisation et de supervision, puis explique comment les combiner spécifiquement pour Azure Digital Twins.

## <a name="review-activity-logs"></a>Examiner les journaux d’activité

Les [journaux d’activité](../azure-monitor/platform/activity-logs-overview.md) Azure fournissent des insights rapides sur les historiques des événements et des opérations de niveau abonnement pour chaque instance du service Azure.

Les événements de niveau abonnement sont les suivants :

* Création de ressources
* Suppression de ressources
* Création de secrets d’application
* Intégration à d’autres services

La journalisation de l’activité pour Azure Digital Twins est activée par défaut et accessible depuis le portail Azure en :

1. Sélectionnant votre instance Azure Digital Twins.
1. Choisissant **Journal d’activité** pour afficher le panneau d’affichage :

    ![Journal d’activité][1]

Pour la journalisation de l’activité avancée :

1. Sélectionnez l’option **Journaux** pour afficher la **Vue d’ensemble de l’analytique des journaux d’activité** :

    ![Sélection][2]

1. La **Vue d’ensemble de l’analytique des journaux d’activité** récapitule les principales données du journal d’activité :

    ![Vue d’ensemble de l’analytique des journaux d’activité][3]

>[!TIP]
>Utilisez **Journaux d’activité** pour obtenir des insights rapides sur les événements de niveau abonnement.

## <a name="enable-customer-diagnostic-logs"></a>Activer les journaux de diagnostic des clients

Vous pouvez définir des [paramètres de diagnostic](../azure-monitor/platform/diagnostic-logs-overview.md) Azure pour chaque instance Azure afin de compléter la journalisation de l’activité. Bien que les journaux d’activité se rapportent aux événements de niveau abonnement, la journalisation des diagnostics fournit des insights sur l’historique opérationnel des ressources elles-mêmes.

Voici quelques exemples de journalisation des diagnostics :

* Heure d’exécution d’une fonction définie par l’utilisateur
* Code d’état de réponse d’une demande d’API réussie
* Récupération d’une clé d’application à partir d’un coffre

Pour activer les journaux de diagnostic pour une instance :

1. Affichez la ressource dans le portail Azure.
1. Cliquez sur **Paramètres de diagnostic** :

    ![Paramètres de diagnostic (1)][4]

1. Cliquez sur **Activer les diagnostics** pour collecter des données (s’ils n’étaient pas activés).
1. Renseignez les champs demandés et sélectionnez comment et où les données doivent être enregistrées :

    ![Paramètres de diagnostic (2)][5]

    Les journaux de diagnostic sont souvent enregistrés à l’aide de [Stockage Fichier Azure](../storage/files/storage-files-deployment-guide.md) et partagés avec [Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md). Les deux options peuvent être sélectionnées.

>[!TIP]
>Utilisez des **journaux de diagnostic** pour connaître les opérations de ressource.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor et Log Analytics

Les applications IoT unifient des ressources, des appareils, des emplacements et des données disparates. La journalisation affinée fournit des informations détaillées sur chaque élément, service ou composant de l’architecture globale de l’application, mais une vue d’ensemble unifiée est souvent nécessaire pour la maintenance et le débogage.

Azure Monitor inclut le puissant service Log Analytics, qui permet de voir et d’analyser les sources de journalisation dans un seul emplacement. Azure Monitor est donc très utile pour l’analyse des journaux dans des applications IoT sophistiquées.

Voici quelques exemples d’utilisation :

* Interrogation de plusieurs historiques de journal de diagnostic
* Visualisation des journaux relatifs à plusieurs fonctions définies par l’utilisateur
* Affichage des journaux relatifs à deux ou plusieurs services dans un intervalle de temps spécifique

[Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) prend en charge l’interrogation complète des journaux. Pour configurer ces fonctionnalités puissantes :

1. Recherchez **Log Analytics** dans le portail Azure.
1. Vos instances **Log Analytics** disponibles apparaissent. Choisissez-en une et sélectionnez les **Journaux** à interroger :

    ![Log Analytics][6]

1. Si vous n’avez pas d’instance **Log Analytics**, vous pouvez créer un espace de travail en cliquant sur le bouton **Ajouter** :

    ![Créer un espace de travail OMS][7]

Une fois votre instance **Log Analytics** provisionnée, vous pouvez utiliser des requêtes puissantes pour rechercher des entrées dans plusieurs journaux ou effectuer une recherche à l’aide de critères spécifiques au moyen de la **Gestion des journaux** :

   ![Gestion du journal][8]

Pour plus d’informations sur les opérations de requêtes puissantes, consultez [Bien démarrer avec les requêtes](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Le premier envoi d’événements à **Log Analytics** peut prendre 5 minutes.

Azure Log Analytics fournit également des services de notification d’erreur et d’alerte puissants, que vous pouvez voir en cliquant sur **Diagnostiquer et résoudre les problèmes** :

   ![Notifications d’alerte et d’erreur][9]

>[!TIP]
>Utilisez **Log Analytics** pour interroger les historiques de journal de plusieurs services, abonnements ou fonctionnalités d’application.

## <a name="other-options"></a>Autres options

Azure Digital Twins prend également en charge la journalisation et l’audit de sécurité spécifiques à une application. Pour une présentation complète de toutes les options de journalisation Azure disponibles pour votre instance Azure Digital Twins, consultez l’article [Journalisation et audit Azure](../security/azure-log-audit.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur les [journaux d’activité](../azure-monitor/platform/activity-logs-overview.md) Azure.

- Approfondissez votre connaissance des paramètres de diagnostic Azure en lisant une [présentation des journaux de diagnostic](../azure-monitor/platform/diagnostic-logs-overview.md).

- Découvrez-en plus sur [Azure Log Analytics](../azure-monitor/log-query/get-started-portal.md).

<!-- Images -->
[1]: media/how-to-configure-monitoring/activity-log.png
[2]: media/how-to-configure-monitoring/activity-log-select.png
[3]: media/how-to-configure-monitoring/log-analytics-overview.png
[4]: media/how-to-configure-monitoring/diagnostic-settings-one.png
[5]: media/how-to-configure-monitoring/diagnostic-settings-two.png
[6]: media/how-to-configure-monitoring/log-analytics.png
[7]: media/how-to-configure-monitoring/log-analytics-oms.png
[8]: media/how-to-configure-monitoring/log-analytics-management.png
[9]: media/how-to-configure-monitoring/log-analytics-notifications.png
