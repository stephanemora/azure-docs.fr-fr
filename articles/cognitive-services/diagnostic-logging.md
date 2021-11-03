---
title: la journalisation des diagnostics.
titleSuffix: Azure Cognitive Services
description: Ce guide fournit des instructions pas à pas pour activer la journalisation des diagnostics pour un service Azure Cognitive Services. Ces journaux fournissent des informations riches et fréquentes sur l’exploitation d’une ressource et permettent l’identification et le débogage des problèmes.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: pafarley
ms.openlocfilehash: 4c621fb7abc494247c9d736639766e6b7c095d8e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080495"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Activer la journalisation des diagnostics pour Azure Cognitive Services

Ce guide fournit des instructions pas à pas pour activer la journalisation des diagnostics pour un service Azure Cognitive Services. Ces journaux fournissent des informations riches et fréquentes sur l’exploitation d’une ressource et permettent l’identification et le débogage des problèmes. Avant de continuer, vous devez disposer d’un compte Azure avec un abonnement à au moins un service cognitif, comme [Speech Services](./speech-service/overview.md) ou [LUIS](./luis/what-is-luis.md).

## <a name="prerequisites"></a>Prérequis

Pour activer la journalisation des diagnostics, vous aurez besoin d’un emplacement pour stocker vos données de journal. Ce tutoriel utilise Stockage Azure et Log Analytics.

* [Stockage Azure](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) – Conserve les journaux de diagnostic pour l’audit de stratégie, l’analyse statique ou la sauvegarde. Il n’est pas nécessaire que le compte de stockage se trouve dans le même abonnement que la ressource générant des journaux d’activité, à condition que l’utilisateur qui configure le paramètre ait un accès Azure RBAC approprié aux deux abonnements.
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) – Outil analytique et de recherche dans les journaux flexible qui permet d’analyser les journaux bruts générés par une ressource Azure.

> [!NOTE]
> * Des options de configuration supplémentaires sont disponibles. Pour en savoir plus, consultez [Collecter et utiliser des données de journaux à partir de vos ressources Azure](../azure-monitor/essentials/platform-logs-overview.md).
> * Une « trace » dans la journalisation des diagnostics n’est disponible que pour les [Réponses aux questions personnalisées](./qnamaker/how-to/get-analytics-knowledge-base.md?tabs=v2).

## <a name="enable-diagnostic-log-collection"></a>Activer la collecte des journaux de diagnostic  

Commençons par activer la journalisation des diagnostics à partir du portail Azure.

> [!NOTE]
> Pour activer cette fonctionnalité à l’aide de PowerShell ou d’Azure CLI, suivez les instructions fournies dans [Collecter et utiliser des données de journaux à partir de vos ressources Azure](../azure-monitor/essentials/platform-logs-overview.md).

1. Accédez au portail Azure. Recherchez ensuite une ressource Cognitive Services et sélectionnez-la. Par exemple, votre abonnement à Speech Services.   
2. Ensuite, dans le menu de navigation de gauche, recherchez **Supervision**, puis sélectionnez **Paramètres de diagnostic**. Cet écran contient tous les paramètres de diagnostic créés précédemment pour cette ressource.
3. Si vous souhaitez utiliser une ressource créée précédemment, vous pouvez la sélectionner maintenant. Sinon, sélectionnez **+ Ajouter un paramètre de diagnostic**.
4. Attribuez un nom au paramètre. Sélectionnez ensuite **Archiver dans un compte de stockage**, puis **Envoyer à Log Analytics**.
5. Quand vous êtes invité à procéder à la configuration, sélectionnez le compte de stockage et l’espace de travail OMS dont vous voulez vous servir pour stocker les journaux de diagnostic. **Remarque** : Si vous n’avez pas de compte de stockage ni d’espace de travail OMS, suivez les invites pour en créer un.
6. Sélectionnez **Audit**, **RequestResponse**, et **AllMetrics**. Définissez ensuite la période de rétention de vos données de journal de diagnostic. Si la stratégie de rétention est définie sur zéro, les événements de cette catégorie de journal sont stockés indéfiniment.
7. Cliquez sur **Enregistrer**.

Jusqu’à deux heures peuvent être nécessaires avant de pouvoir interroger et analyser les données de journalisation. Autrement dit, ne vous inquiétez pas si vous ne voyez rien dans un premier temps.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Afficher et exporter des données de diagnostic à partir de Stockage Azure

Stockage Azure est une solution de stockage d’objets robuste qui est optimisée pour stocker de grandes quantités de données non structurées. Dans cette section, vous allez apprendre à rechercher les transactions de plus de 30 jours dans votre compte de stockage et à exporter les données vers Excel.

1. Sur le portail Azure, recherchez la ressource Stockage Azure que vous avez créée dans la dernière section.
2. Dans le menu de navigation de gauche, recherchez **Supervision**, puis sélectionnez **Métriques**.
3. Servez-vous des listes déroulantes disponibles pour configurer votre requête. Pour cet exemple, nous allons définir l’intervalle de temps sur **30 derniers jours** et la métrique sur **Transaction**.
4. Une fois la requête terminée, vous obtenez une visualisation des transactions des 30 derniers jours. Pour exporter ces données, utilisez le bouton **Exporter vers Excel** situé en haut de la page.

Découvrez plus en détail ce que vous pouvez faire des données de diagnostic dans [Stockage Azure](../storage/blobs/storage-blobs-introduction.md).

## <a name="view-logs-in-log-analytics"></a>Afficher les journaux d’activité dans Log Analytics

Suivez ces instructions pour explorer les données Log Analytics pour votre ressource.

1. Sur le portail Azure, recherchez et sélectionnez **Log Analytics** dans le menu de navigation de gauche.
2. Recherchez et sélectionnez la ressource que vous avez créée quand vous avez activé les diagnostics.
3. Sous **Général**, recherchez et sélectionnez **Journaux**. À partir de cette page, vous pouvez exécuter des requêtes sur vos journaux.

### <a name="sample-queries"></a>Exemples de requêtes

Voici quelques requêtes Kusto simples dont vous pouvez vous servir pour explorer vos données de journal.

Exécutez cette requête pour tous les journaux de diagnostic issus d’Azure Cognitive Services pour une période déterminée :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Exécutez cette requête pour afficher les 10 journaux les plus récents :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Exécutez cette requête pour regrouper les opérations par **Ressource** :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Exécutez cette requête pour déterminer la durée moyenne d’une opération :

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Exécutez cette requête pour afficher le volume d’opérations dans le temps par OperationName avec des décomptes compartimentés par périodes de 10 secondes.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment activer la journalisation et quelles sont les métriques et les catégories de journal prises en charge par les différents services Azure, consultez les articles [Vue d’ensemble des métriques dans Microsoft Azure](../azure-monitor/data-platform.md) et [Présentation des journaux de diagnostic Azure](../azure-monitor/essentials/platform-logs-overview.md).
* Pour en savoir plus sur les concentrateurs d’événements, lisez les articles suivants :
  * [Nouveautés des concentrateurs d’événements Azure ?](../event-hubs/event-hubs-about.md)
  * [Prise en main des hubs d’événements](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)
* Consultez la [Vue d’ensemble des recherches dans les journaux Azure Monitor](../azure-monitor/logs/log-query-overview.md).