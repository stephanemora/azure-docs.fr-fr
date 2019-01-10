---
title: Modèle de données d’Azure Application Insights | Microsoft Docs
description: Décrit les propriétés exportées à partir de l’exportation continue dans JSON et utilisées comme filtres.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: cabad41c-0518-4669-887f-3087aef865ea
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: mbullwin
ms.openlocfilehash: 12025dfb93bbcfc86ae301f8fb63e7ac74697cf2
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119270"
---
# <a name="application-insights-export-data-model"></a>Modèle d’exportation de données Application Insights
Cette table répertorie les propriétés de télémétrie envoyées à partir des Kits SDK [Application Insights](../../azure-monitor/app/app-insights-overview.md) au portail.
Vous verrez ces propriétés dans les données issues d’une [exportation continue](export-telemetry.md).
Elles apparaissent également dans les filtres de propriétés, dans [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) et dans [Recherche de diagnostic](../../azure-monitor/app/diagnostic-search.md).

Points à noter :

* `[0]` dans ces tables désigne un point dans le chemin d’accès au niveau duquel vous devez insérer un index ; il ne s’agit pas toujours de 0.
* Les durées sont énoncées en dixièmes de microsecondes, donc 10000000 = 1 seconde.
* Les dates et les heures sont indiquées au format UTC, et respectent la norme ISO `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Exemples
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Context
Tous les types de données de télémétrie sont accompagnés d’une section de contexte. Tous ces champs ne sont pas transmis avec tous les points de données.

| path | type | Notes |
| --- | --- | --- |
| context.custom.dimensions [0] |objet [ ] |Paires clé-valeur définies par le paramètre des propriétés personnalisées. Longueur maximale de clé 100, longueur maximale de valeur 1024. Plus de 100 valeurs uniques, la propriété peut faire l’objet d’une recherche, mais ne peut pas être utilisée pour la segmentation. 200 clés maximum par ikey. |
| context.custom.metrics [0] |objet [ ] |Paires clé-valeur définies par le paramètre des mesures personnalisées et par TrackMetrics. Longueur maximale de clé 100, les valeurs peuvent être numériques. |
| context.data.eventTime |chaîne |UTC |
| context.data.isSynthetic |booléenne |Requête transmise par un robot ou un test web. |
| context.data.samplingRate |number |Pourcentage de télémétrie générée par le Kit SDK qui est envoyé vers le portail. Plage 0.0-100.0. |
| context.device |objet |Appareil client |
| context.device.browser |chaîne |IE, Chrome, ... |
| context.device.browserVersion |chaîne |Chrome 48.0, ... |
| context.device.deviceModel |chaîne | |
| context.device.deviceName |chaîne | |
| context.device.id |chaîne | |
| context.device.locale |chaîne |en-GB, de-DE, ... |
| context.device.network |chaîne | |
| context.device.oemName |chaîne | |
| context.device.os |chaîne | |
| context.device.osVersion |chaîne |Système d’exploitation hôte |
| context.device.roleInstance |chaîne |ID de l’hôte du serveur |
| context.device.roleName |chaîne | |
| context.device.screenResolution |chaîne | |
| context.device.type |chaîne |PC, navigateur... |
| context.location |objet |Dérivé de clientip. |
| context.location.city |chaîne |Dérivé de clientip, si connu |
| context.location.clientip |chaîne |Dernier octogone anonyme (0). |
| context.location.continent |chaîne | |
| context.location.country |chaîne | |
| context.location.province |chaîne |État ou province |
| context.operation.id |chaîne |Les éléments qui affichent le même ID d’opération sont représentés en tant qu’éléments associés dans le portail. Généralement l’ID de requête. |
| context.operation.name |chaîne |nom d’URL ou de requête |
| context.operation.parentId |chaîne |Autorise les éléments liés imbriqués. |
| context.session.id |chaîne |ID d’un groupe d’opérations de la même source. Une période de 30 minutes sans opération signale la fin d’une session. |
| context.session.isFirst |booléenne | |
| context.user.accountAcquisitionDate |chaîne | |
| context.user.accountId |chaîne | |
| context.user.anonAcquisitionDate |chaîne | |
| context.user.anonId |chaîne | |
| context.user.authAcquisitionDate |chaîne |[Utilisateur authentifié](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |chaîne | |
| context.user.isAuthenticated |booléenne | |
| context.user.storeRegion |chaîne | |
| internal.data.documentVersion |chaîne | |
| internal.data.id |chaîne | ID unique assigné lorsqu’un élément est ingéré dans Application Insights |

## <a name="events"></a>Événements
Événements personnalisés générés par [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| path | type | Notes |
| --- | --- | --- |
| event [0] count |integer |100 / (taux d’[échantillonnage](../../azure-monitor/app/sampling.md) ). Par exemple, 4 =&gt; 25 %. |
| event [0] name |chaîne |Nom de l’événement.  Longueur maximale 250. |
| event [0] url |chaîne | |
| event [0] urlData.base |chaîne | |
| event [0] urlData.host |chaîne | |

## <a name="exceptions"></a>Exceptions
Signale des [exceptions](../../azure-monitor/app/asp-net-exceptions.md) sur le serveur et dans le navigateur.

| path | type | Notes |
| --- | --- | --- |
| basicException [0] assembly |chaîne | |
| basicException [0] count |integer |100 / (taux d’[échantillonnage](../../azure-monitor/app/sampling.md) ). Par exemple, 4 =&gt; 25 %. |
| basicException [0] exceptionGroup |chaîne | |
| basicException [0] exceptionType |chaîne | |
| basicException [0] failedUserCodeMethod |chaîne | |
| basicException [0] failedUserCodeAssembly |chaîne | |
| basicException [0] handledAt |chaîne | |
| basicException [0] hasFullStack |booléenne | |
| basicException [0] id |chaîne | |
| basicException [0] method |chaîne | |
| basicException [0] message |chaîne |Message d’exception. Longueur maximale 10 000. |
| basicException [0] outerExceptionMessage |chaîne | |
| basicException [0] outerExceptionThrownAtAssembly |chaîne | |
| basicException [0] outerExceptionThrownAtMethod |chaîne | |
| basicException [0] outerExceptionType |chaîne | |
| basicException [0] outerId |chaîne | |
| basicException [0] parsedStack [0] assembly |chaîne | |
| basicException [0] parsedStack [0] fileName |chaîne | |
| basicException [0] parsedStack [0] level |integer | |
| basicException [0] parsedStack [0] line |integer | |
| basicException [0] parsedStack [0] method |chaîne | |
| basicException [0] stack |chaîne |Longueur maximale 10 000 |
| basicException [0] typeName |chaîne | |

## <a name="trace-messages"></a>Messages de suivi
Envoyé par [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) et par les [adaptateurs de journalisation](../../azure-monitor/app/asp-net-trace-logs.md).

| path | type | Notes |
| --- | --- | --- |
| message [0] loggerName |chaîne | |
| message [0] parameters |chaîne | |
| message [0] raw |chaîne |Le message du fichier journal, longueur maximale 10k. |
| message [0] severityLevel |chaîne | |

## <a name="remote-dependency"></a>Dépendance distante
Envoyé par TrackDependency. Utilisé pour consigner les performances et l’utilisation des [appels aux dépendances](../../azure-monitor/app/asp-net-dependencies.md) dans le serveur, et des appels AJAX dans le navigateur.

| path | type | Notes |
| --- | --- | --- |
| remoteDependency [0] async |booléenne | |
| remoteDependency [0] baseName |chaîne | |
| remoteDependency [0] commandName |chaîne |Par exemple, « home/index » |
| remoteDependency [0] count |integer |100 / (taux d’[échantillonnage](../../azure-monitor/app/sampling.md) ). Par exemple, 4 =&gt; 25 %. |
| remoteDependency [0] dependencyTypeName |chaîne |HTTP, SQL, ... |
| remoteDependency [0] durationMetric.value |number |Délai de l’appel à la fin de la réponse par la dépendance |
| remoteDependency [0] id |chaîne | |
| remoteDependency [0] name |chaîne |Url. Longueur maximale 250. |
| remoteDependency [0] resultCode |chaîne |à partir de la dépendance HTTP |
| remoteDependency [0] success |booléenne | |
| remoteDependency [0] type |chaîne |HTTP, SQL, ... |
| remoteDependency [0] url |chaîne |Longueur maximale 2 000 |
| remoteDependency [0] urlData.base |chaîne |Longueur maximale 2 000 |
| remoteDependency [0] urlData.hashTag |chaîne | |
| remoteDependency [0] urlData.host |chaîne |Longueur maximale 200 |

## <a name="requests"></a>Demandes
Envoyées par [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Les modules standard les utilisent pour consigner le temps de réponse du serveur, mesuré sur le serveur.

| path | type | Notes |
| --- | --- | --- |
| request [0] count |integer |100 / (taux d’[échantillonnage](../../azure-monitor/app/sampling.md) ). Par exemple :  4 =&gt; 25 %. |
| request [0] durationMetric.value |number |Délai entre l’arrivée de la requête et la réponse. 1e7 = 1s |
| request [0] id |chaîne |ID d’opération |
| request [0] name |chaîne |GET/POST + base d’URL  Longueur maximale 250 |
| request [0] responseCode |integer |Réponse HTTP envoyée au client |
| request [0] success |booléenne |Par défaut == (responseCode &lt; 400) |
| request [0] url |chaîne |Sans hôte |
| request [0] urlData.base |chaîne | |
| request [0] urlData.hashTag |chaîne | |
| request [0] urlData.host |chaîne | |

## <a name="page-view-performance"></a>Performances d’affichage de la page
Envoyées par le navigateur. Mesure le temps de traitement d’une page, du lancement de la requête par l’utilisateur à l’affichage complet (sans les appels asynchrones AJAX).

Les valeurs de contexte représentent la version de système d’exploitation et de navigateur du client.

| path | type | Notes |
| --- | --- | --- |
| clientPerformance [0] clientProcess.value |integer |Délai entre la fin de la réception du code HTML et l’affichage de la page. |
| clientPerformance [0] name |chaîne | |
| clientPerformance [0] networkConnection.value |integer |Temps nécessaire pour l’établissement d’une connexion réseau. |
| clientPerformance [0] receiveRequest.value |integer |Délai entre la fin de l’envoi d’une requête et la réception du code HTML en réponse. |
| clientPerformance [0] sendRequest.value |integer |Délai nécessaire à l’envoi de la requête HTTP. |
| clientPerformance [0] total.value |integer |Délai entre le début d’envoi de la requête et l’affichage de la page. |
| clientPerformance [0] url |chaîne |URL de cette requête. |
| clientPerformance [0] urlData.base |chaîne | |
| clientPerformance [0] urlData.hashTag |chaîne | |
| clientPerformance [0] urlData.host |chaîne | |
| clientPerformance [0] urlData.protocol |chaîne | |

## <a name="page-views"></a>Affichages de pages
Envoyé par trackPageView() ou [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| path | type | Notes |
| --- | --- | --- |
| view [0] count |integer |100 / (taux d’[échantillonnage](../../azure-monitor/app/sampling.md) ). Par exemple, 4 =&gt; 25 %. |
| view [0] durationMetric.value |integer |Valeur éventuellement définie dans trackPageView() ou par startTrackPage() - stopTrackPage(). Pas identique aux valeurs clientPerformance. |
| view [0] name |chaîne |Titre de la page.  Longueur maximale 250 |
| view [0] url |chaîne | |
| view [0] urlData.base |chaîne | |
| view [0] urlData.hashTag |chaîne | |
| view [0] urlData.host |chaîne | |

## <a name="availability"></a>Disponibilité
Consigne les [tests web de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md).

| path | type | Notes |
| --- | --- | --- |
| availability [0] availabilityMetric.name |chaîne |Availability |
| availability [0] availabilityMetric.value |number |1.0 ou 0.0 |
| availability [0] count |integer |100 / (taux d’[échantillonnage](../../azure-monitor/app/sampling.md) ). Par exemple, 4 =&gt; 25 %. |
| availability [0] dataSizeMetric.name |chaîne | |
| availability [0] dataSizeMetric.value |integer | |
| availability [0] durationMetric.name |chaîne | |
| availability [0] durationMetric.value |number |Durée du test. 1e7 = 1s |
| availability [0] message |chaîne |Diagnostic de défaillance |
| availability [0] result |chaîne |Réussite/Échec |
| availability [0] runLocation |chaîne |Source géographique de la requête HTTP |
| availability [0] testName |chaîne | |
| availability [0] testRunId |chaîne | |
| availability [0] testTimestamp |chaîne | |

## <a name="metrics"></a>Mesures
Généré par TrackMetric().

La valeur de la métrique se trouve dans context.custom.metrics[0]

Par exemple : 

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>À propos des valeurs de mesure
Les valeurs de mesure, dans les rapports de mesure et ailleurs, sont consignées avec une structure d’objet standard. Par exemple : 

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Actuellement (cela peut changer à l’avenir), dans l’ensemble des valeurs consignées des modules standard du SDK, `count==1` et uniquement les champs `name` et `value` sont utiles. Le seul cas où la situation serait différente serait une configuration où vous écririez vos propres appels TrackMetric, dans lesquels vous définiriez les autres paramètres.

Les autres champs ont vocation à autoriser l’agrégation des mesures dans le Kit SDK afin de réduire le trafic dans le portail. Par exemple, vous pouvez décider d’accumuler un nombre défini de valeurs successives avant d’envoyer chaque rapport de mesure. Vous calculeriez ensuite la valeur minimale, la valeur maximale, l’écart standard et la valeur agrégée (somme ou moyenne) et définir le décompte sur le nombre de valeurs représentées par le rapport.

Dans les tableaux ci-dessus, nous avons volontairement omis les champs rarement utilisés count, min, max, stdDev et sampledValue.

Au lieu de pré-agréger les mesures, vous pouvez utiliser l’ [échantillonnage](../../azure-monitor/app/sampling.md) si vous avez besoin de réduire le volume de télémétrie.

### <a name="durations"></a>Durées
Sauf mention contraire, les durées sont indiquées en dixièmes de microseconde. Ainsi, 10000000.0 représente 1s.

## <a name="see-also"></a>Voir aussi
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Exportation continue](export-telemetry.md)
* [Exemples de code](export-telemetry.md#code-samples)
