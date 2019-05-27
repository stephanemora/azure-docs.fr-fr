---
title: Guide pratique pour configurer la supervision dans Azure Digital Twins | Microsoft Docs
description: Guide pratique pour configurer la supervision dans Azure Digital Twins.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: v-adgera
ms.custom: seodec18
ms.openlocfilehash: 3c35633c9b25eafdb738ed591c7c7022fbd8149a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967823"
---
# <a name="how-to-configure-monitoring-in-azure-digital-twins"></a>Guide pratique pour configurer la supervision dans Azure Digital Twins

Azure Digital Twins prend en charge de solides fonctionnalités de journalisation, de supervision et d’analytique. Les développeurs de solutions peuvent utiliser des journaux Azure Monitor, journaux de diagnostic, journal d’activité et d’autres services pour prendre en charge les besoins d’analyse complexes d’une application IoT. Vous pouvez combiner les options de journalisation afin d’interroger ou d’afficher des enregistrements entre plusieurs services et de fournir une couverture de journalisation précise pour de nombreux services.

Cet article récapitule les options de journalisation et de supervision, puis explique comment les combiner spécifiquement pour Azure Digital Twins.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

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

1. Sélectionnez l’option **Journaux d’activité** pour afficher la **Vue d’ensemble d’Activity Log Analytics** :

    ![Sélection][2]

1. La **Vue d’ensemble de l’analytique des journaux d’activité** récapitule les principales données du journal d’activité :

    ![Vue d’ensemble de Log Analytics][3]

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

    Journaux de diagnostic sont souvent enregistrés à l’aide de [stockage fichier Azure](../storage/files/storage-files-deployment-guide.md) et partagées avec [Azure Monitor enregistre](../azure-monitor/log-query/get-started-portal.md). Les deux options peuvent être sélectionnées.

>[!TIP]
>Utilisez des **journaux de diagnostic** pour connaître les opérations de ressource.

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor et Log Analytics

Les applications IoT unifient des ressources, des appareils, des emplacements et des données disparates. La journalisation affinée fournit des informations détaillées sur chaque élément, service ou composant de l’architecture globale de l’application, mais une vue d’ensemble unifiée est souvent nécessaire pour la maintenance et le débogage.

Azure Monitor inclut le service d’analytique de journaux puissant, qui permet la journalisation des sources pour afficher et analyser dans un emplacement. Azure Monitor est donc très utile pour l’analyse des journaux d’activité dans des applications IoT sophistiquées.

Voici quelques exemples d’utilisation :

* Interrogation de plusieurs historiques de journal de diagnostic
* Visualisation des journaux d’activité relatifs à plusieurs fonctions définies par l’utilisateur
* Affichage des journaux d’activité relatifs à deux ou plusieurs services dans un intervalle de temps spécifique

Interrogation du journal complet est fourni via [Azure Monitor enregistre](../azure-monitor/log-query/log-query-overview.md). Pour configurer ces fonctionnalités puissantes :

1. Recherchez **Log Analytics** dans le portail Azure.
1. Vous verrez votre disponible **espace de travail Analytique de journal** instances. Choisissez-en une et sélectionnez les **Journaux d’activité** à interroger :

    ![Log Analytics][6]

1. Si vous n’avez pas déjà un **espace de travail Analytique de journal** instance, vous pouvez créer un espace de travail en cliquant sur le **ajouter** bouton :

    ![Créer un espace de travail OMS][7]

Une fois votre **espace de travail Analytique de journal** instance est approvisionnée, vous pouvez utiliser des requêtes puissantes pour rechercher des entrées dans les journaux de multiples ou à l’aide des critères spécifiques à l’aide de **gestion des journaux**:

   ![Gestion du journal][8]

Pour plus d’informations sur les opérations de requêtes puissantes, consultez [Bien démarrer avec les requêtes](../azure-monitor/log-query/get-started-queries.md).

> [!NOTE]
> Vous pouvez rencontrer un délai de 5 minutes lors de l’envoi d’événements à **espace de travail Analytique de journal** pour la première fois.

Journaux d’Azure Monitor fournit également des erreurs puissante et les services de notification d’alerte, qui peuvent être affichés en cliquant sur **diagnostiquer et résoudre les problèmes**:

   ![Notifications d’alerte et d’erreur][9]

>[!TIP]
>Utilisez **espace de travail Analytique de journal** à historiques de journal de requête pour plusieurs fonctionnalités de l’application, les abonnements ou les services.

## <a name="other-options"></a>Autres options

Azure Digital Twins prend également en charge la journalisation et l’audit de sécurité spécifiques à une application. Pour une présentation complète de toutes les options de journalisation Azure disponibles pour votre instance Azure Digital Twins, consultez l’article [Journalisation et audit Azure](../security/azure-log-audit.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez-en plus sur les [journaux d’activité](../azure-monitor/platform/activity-logs-overview.md) Azure.

- Approfondissez votre connaissance des paramètres de diagnostic Azure en lisant une [présentation des journaux de diagnostic](../azure-monitor/platform/diagnostic-logs-overview.md).

- En savoir plus sur [Azure Monitor enregistre](../azure-monitor/log-query/get-started-portal.md).

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
