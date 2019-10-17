---
title: Analyser les journaux et les métriques dans Azure Spring Cloud | Microsoft Docs
description: Découvrez comment analyser les données de diagnostic dans Azure Spring Cloud.
services: spring-cloud
author: jpconnock
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: e6b90fb09c536f68bee7fd5d57507fe3920bcf1e
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038716"
---
# <a name="analyze-logs-and-metrics-with-diagnostic-settings"></a>Analyser les journaux et les métriques avec les paramètres de diagnostic

La fonctionnalité de diagnostic d’Azure Spring Cloud vous permet d’analyser les journaux et les métriques à l’aide de l’un des services suivants :

* Analysez-les avec Azure Log Analytics, les données sont écrites immédiatement dans Log Analytics sans qu’il soit nécessaire de les écrire au préalable dans le stockage.
* Enregistrez-les dans un compte de stockage pour l’audit ou l’inspection manuelle. Spécifiez la durée de conservation (en jours).
* Envoyez-les à Event Hubs pour ingestion par un service tiers ou une solution d’analytique personnalisée.

Pour commencer, vous devez activer l’un de ces services pour recevoir les données.  Pour en savoir plus sur la configuration de Log Analytics, consultez [ce tutoriel](../azure-monitor/log-query/get-started-portal.md).  

## <a name="configure-diagnostic-settings"></a>Configurer les paramètres de diagnostic

1. Accédez à votre instance Azure Spring Cloud dans le portail Azure.
1. Sélectionnez l’option de menu **Paramètres de diagnostic**.
1. Sélectionnez le bouton **Ajouter un paramètre de diagnostic**.
1. Entrez un nom pour le paramètre et choisissez l’emplacement où vous souhaitez envoyer les journaux. Vous pouvez sélectionner n’importe quelle combinaison des trois options suivantes :
    * Archiver dans un compte de stockage
    * Diffuser vers un hub d’événements
    * Envoyer à Log Analytics

1. Choisissez la catégorie de journal et la catégorie de métrique à superviser, puis spécifiez la durée de conservation (en jours). La durée de conservation s’applique uniquement au compte de stockage.
1. Sélectionnez **Enregistrer** pour appliquer le paramètre.

> [!NOTE]
> Il peut s’écouler jusqu’à 15 minutes entre le moment où les journaux ou les métriques sont émis et le moment où ils apparaissent dans le compte de stockage/Event Hub/Log Analytics.

## <a name="viewing-logs"></a>Consultation des journaux

### <a name="using-log-analytics"></a>Utilisation de Log Analytics

1. Dans le portail Azure, sélectionnez Log Analytics dans le menu de navigation de gauche.
1. Sélectionnez l’espace de travail Log Analytics que vous avez choisi lors de l’ajout des paramètres de diagnostic.
1. Sélectionnez `Logs` pour ouvrir le panneau Recherche dans les journaux.
1. Entrez une requête simple dans la zone Recherche dans les journaux.  Par exemple :

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

1. Sélectionnez `Run` pour afficher les résultats de la recherche.
1. Vous pouvez rechercher les journaux de l’application ou de l’instance spécifique en définissant une condition de filtre :

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

Pour en savoir plus sur le langage de requête utilisé dans Log Analytics, consultez [cet article](../azure-monitor/log-query/query-language.md).

### <a name="using-logs-and-metrics-in-storage-account"></a>Utilisation des journaux et des métriques dans le compte de stockage

1. Dans le portail Azure, sélectionnez Comptes de stockage dans le menu de navigation de gauche.
1. Sélectionnez le compte de stockage que vous avez choisi lors de l’ajout des paramètres de diagnostic.
1. Sélectionnez l’entrée `Blobs` pour ouvrir le panneau Conteneur d’objets blob.
1. Recherchez un conteneur nommé `insights-logs-applicationconsole` pour consulter les journaux des applications.
1. Recherchez un conteneur nommé `insights-metrics-pt1m` pour consulter les métriques des applications.

[Apprenez-en davantage sur l’envoi d’informations de diagnostic à un compte de stockage.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-to-storage)

### <a name="using-event-hubs"></a>Utilisation d’Event Hubs

1. Dans le portail Azure, sélectionnez Event Hubs dans le menu de navigation de gauche.
1. Recherchez et sélectionnez les hubs d’événements que vous avez choisis lors de l’ajout des paramètres de diagnostic.
1. Sélectionnez `Event Hubs` pour ouvrir le panneau Liste des hubs d’événements.
1. Recherchez un hub d’événements nommé `insights-logs-applicationconsole` pour consulter les journaux des applications.
1. Recherchez un hub d’événements nommé `insights-metrics-pt1m` pour consulter les métriques des applications.

[Apprenez-en davantage sur l’envoi d’informations de diagnostic à un hub d’événement.](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs)

## <a name="analyzing-logs"></a>Analyse des journaux

Azure Log Analytics fournit Kusto afin que vous puissiez interroger vos journaux à des fins d’analyse.  Pour obtenir une présentation rapide de l’interrogation des journaux à l’aide de Kusto, passez en revue le [tutoriel sur Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Les journaux d’application fournissent des informations critiques sur l’intégrité de votre application, ses performances, et bien plus encore.  Voici quelques requêtes simples pour vous aider à comprendre les états actuels et passés de votre application.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Afficher les journaux d’application à partir d’Azure Spring Cloud

Pour consulter la liste des journaux d’application à partir d’Azure Spring Cloud, triés par heure avec les derniers journaux affichés en premier :

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Afficher les entrées de journaux contenant des erreurs ou des exceptions

Cette requête vous permet de consulter les entrées de journaux qui mentionnent une erreur ou une exception.  Les résultats ne sont pas triés.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Utilisez cette requête pour rechercher des erreurs, ou modifiez les termes de la requête afin de rechercher des codes d’erreur ou des exceptions spécifiques.  

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Afficher le nombre d’erreurs et d’exceptions signalées par votre application au cours de la dernière heure

Cette requête crée un graphique à secteurs indiquant le nombre d’erreurs et d’exceptions enregistrées par votre application au cours de la dernière heure :

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>En savoir plus sur l’interrogation des journaux d’application

Azure Monitor fournit une prise en charge étendue de l’interrogation des journaux d’application à l’aide de Log Analytics.  Pour en savoir plus sur ce service, consultez le tutoriel sur les [requêtes de journal](../azure-monitor/log-query/get-started-queries.md) à l’aide d’Azure Monitor. La [Vue d’ensemble des requêtes de journal dans Azure Monitor](../azure-monitor/log-query/log-query-overview.md) fournit plus d’informations sur la création de requêtes pour analyser les journaux de vos applications.
