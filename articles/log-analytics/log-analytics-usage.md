---
title: Analyser l’utilisation des données dans Log Analytics | Microsoft Docs
description: Utilisez le tableau de bord répertoriant l’utilisation et les coûts estimés de Log Analytics pour déterminer la quantité de données envoyées à Log Analytics et identifier les éléments à l’origine d’augmentations inattendues.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: e702e1f5eb1816b007317765e4c9a9f88bb99bfd
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52635422"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analyser l’utilisation des données dans Log Analytics

> [!NOTE]
> Cet article explique comment analyser l’utilisation des données dans Log Analytics.  Pour plus d’informations, consultez les articles suivants.
> - L’article [Manage cost by controlling data volume and retention in Log Analytics](log-analytics-manage-cost-storage.md) (Gérer les coûts en contrôlant le volume et la conservation des données dans Log Analytics) explique comment contrôler ses coûts en modifiant la durée de conservation des données.
> - L’article [Monitoring usage and estimated costs](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) (Surveillance de l’utilisation et estimation des coûts) explique comment visualiser l’utilisation et les coûts estimés avec plusieurs fonctions de surveillance Azure en fonction des différents modèles de tarification. Il explique également comment modifier votre modèle de tarification.

## <a name="understand-usage"></a>Comprendre l’utilisation

Utilisez le tableau de bord **Utilisation de Log Analytics et coûts estimés** pour examiner et analyser l’utilisation de données. Ce tableau montre la quantité de données collectée par chaque solution, la quantité de données conservée, et fournit une estimation des coûts en fonction de la quantité de données ingérées et de toute rétention supplémentaire au-delà du montant inclus.

![Utilisation et estimation des coûts](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>

Pour explorer vos données plus en détail, cliquez sur l’icône en haut à droite d’un des graphiques sur la page **Utilisation et estimation des coûts**. Vous pouvez maintenant utiliser cette requête pour obtenir de plus amples informations sur votre utilisation.  

![Vue Journaux](media/log-analytics-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Résolution des problèmes à l’origine d’une utilisation plus importante que prévu
Une utilisation plus importante est due à l’un des éléments suivants, voire les deux :
- Plus de données que prévu sont envoyées à Log Analytics
- Plus de nœuds que prévu sont envoyés à Log Analytics ou certains nœuds envoient plus de données que d’habitude

Examinons la façon dont nous pouvons en savoir plus sur ces deux causes. 

> [!NOTE]
> Certains champs du type de données Utilisation, bien que faisant partie du schéma, sont maintenant déconseillés et leurs valeurs ne sont plus fournies. Il s’agit de **Computer** et des champs liées à l’ingestion (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** et **AverageProcessingTimeMs**.

### <a name="data-volume"></a>Volume de données 
Sur la page **Utilisation et estimation des coûts**, le graphique *Ingestion de données par solution* montre le volume total des données envoyées et la quantité envoyée par chaque solution. Vous pouvez ainsi dégager des tendances, par exemple si l’utilisation des données globales (ou l’utilisation par une solution particulière) augmente, reste stable ou diminue. La requête utilisée pour générer ce résultat est

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Notez que la clause « where IsBillable = true » exclut les types de données de certaines solutions pour lesquels il n’existe aucun frais d’ingestion. 

Vous pouvez explorer de façon plus précise et déterminer ainsi des tendances pour des types de données spécifiques, par exemple si vous souhaitez étudier les données de journaux IIS :

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Envoi de données par les nœuds

Pour plus d’informations sur le nombre de nœuds qui ont envoyé des données le mois dernier, utilisez

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

Pour afficher le nombre d’événements reçus par ordinateur, utilisez

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Utilisez cette requête avec parcimonie car son exécution est coûteuse. Pour afficher le nombre d’événements facturables reçus par ordinateur, utilisez 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Si vous souhaitez afficher les types de données facturables qui ont envoyé des données à un ordinateur spécifique, utilisez :

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

Pour explorer plus en détail la source de données d’un type de données particulier, voici quelques exemples de requêtes :

+ une solution de **sécurité**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ une solution de **gestion des journaux**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ le type de données **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ le type de données **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ le type de données **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ le type de données **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Conseils pour réduire le volume de données

Voici quelques suggestions pour réduire le volume de journaux collectés :

| Source du volume de données important | Comment réduire le volume de données |
| -------------------------- | ------------------------- |
| Événements de sécurité            | Sélectionnez [les événements de sécurité courants ou minimaux](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Modifier la stratégie d’audit de sécurité pour collecter les événements nécessaires uniquement. Plus particulièrement, examinez la nécessité de collecter des événements pour : <br> - [plateforme de filtrage de l’audit](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [registre de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [système de fichiers de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [objet de noyau d’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [manipulation du descripteur de l’audit](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - stockage amovible de l’audit |
| Compteurs de performances       | Modifiez la [configuration du compteur de performances](log-analytics-data-sources-performance-counters.md) de façon à : <br> - Réduire la fréquence de collecte <br> - Réduire le nombre de compteurs de performance |
| Journaux d’événements                 | Modifiez la [configuration du journal d’événements](log-analytics-data-sources-windows-events.md) de façon à : <br> - Réduire le nombre de journaux des événements collectés <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations*. |
| syslog                     | Modifiez la [configuration du syslog](log-analytics-data-sources-syslog.md) de façon à : <br> - Réduire le nombre d’installations collectées <br> - Collecter uniquement les niveaux d’événement requis Par exemple, ne collectez pas les événements de niveau *Informations* et *Débogage*. |
| AzureDiagnostics           | Modifiez la collection de journaux de ressources pour : <br> - Réduire le nombre de journaux d’envoi de ressources à Log Analytics <br> - Collecter uniquement les journaux nécessaires |
| Données de solution d’ordinateurs n’ayant pas besoin de la solution | Utilisez le [ciblage de solution](../azure-monitor/insights/solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement. |

### <a name="getting-node-counts"></a>Calcul du nombre de nœuds 

Si vous utilisez un niveau tarifaire « Par nœud (OMS) », vous êtes facturé en fonction du nombre de nœuds et de solutions que vous utilisez, et le nombre de nœuds Insights et Analytics pour lesquels vous êtes facturé s’affichera dans la table à la page **Utilisation et estimation des coûts**.  

Pour afficher le nombre de nœuds Security distincts, vous pouvez utiliser la requête :

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Pour afficher le nombre de nœuds Automation distincts, utilisez la requête :

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Créer une alerte lorsque la collection de données est plus volumineuse que prévu
Cette section décrit la création d’une alerte si :
- Le volume de données dépasse une quantité spécifiée.
- Le volume de données est censé dépasser une quantité spécifiée.

Les Alertes Azure prennent en charge les [alertes de journal](../monitoring-and-diagnostics/monitor-alerts-unified-log.md) qui utilisent des requêtes de recherche. 

La requête suivante obtient un résultat quand plus de 100 Go de données sont collectés dans les dernières 24 heures :

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

La requête suivante utilise une formule simple pour prévoir le moment où plus de 100 Go de données seront envoyés en une journée : 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Pour alerter sur un volume de données différent, remplacez la valeur de 100 dans les requêtes par le nombre de Go pour lequel vous souhaitez créer une alerte.

Utilisez les étapes décrites dans [Création d’une alerte de journal](../monitoring-and-diagnostics/alert-metric.md) pour être averti lorsque la collection de données est plus volumineuse que prévu.

Lors de la création de l’alerte pour la première requête, lorsque plus de 100 Go de données sont collectés en 24 heures, définissez :  

- **Définir la condition d’alerte** spécifiez votre espace de travail Log Analytics comme cible de la ressource.
- **Critères d’alerte** spécifiez les éléments suivants :
   - **Nom du signal** sélectionnez **Recherche de journal personnalisée**
   - **Requête de recherche** sur `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - La **logique d’alerte** est **basée sur**  le *nombre de résultats* et **Condition** est *supérieur à* un **seuil**  de *0*
   - **Période de temps** de *1440* minutes et **fréquence des alertes** toutes les *60* minutes comme les données d’utilisation ne se mettent à jour qu’une fois par heure.
- **Définir les détails de l’alerte** spécifiez les éléments suivants :
   - **Nom** sur *Data volume greater than 100 GB in 24 hours* (Volume de données supérieur à 10 Go en 24 heures)
   - **Gravité** : *Avertissement*

Spécifiez un [groupe d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) existant ou créez-en un nouveau afin que l’alerte de journal corresponde aux critères, vous êtes informé.

Lors de la création de l’alerte pour la seconde requête, lorsqu’il est prévu que plus de 100 Go de données seront collectés en 24 heures, définissez :

- **Définir la condition d’alerte** spécifiez votre espace de travail Log Analytics comme cible de la ressource.
- **Critères d’alerte** spécifiez les éléments suivants :
   - **Nom du signal** sélectionnez **Recherche de journal personnalisée**
   - **Requête de recherche** sur `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - La **logique d’alerte** est **basée sur**  le *nombre de résultats* et **Condition** est *supérieur à* un **seuil**  de *0*
   - **Période de temps** de *180* minutes et **fréquence des alertes** toutes les *60* minutes comme les données d’utilisation ne se mettent à jour qu’une fois par heure.
- **Définir les détails de l’alerte** spécifiez les éléments suivants :
   - **Nom** à *Data volume expected to greater than 100 GB in 24 hours* (Volume de données attendu supérieur à 10 Go en 24 heures)
   - **Gravité** : *Avertissement*

Spécifiez un [groupe d’actions](../monitoring-and-diagnostics/monitoring-action-groups.md) existant ou créez-en un nouveau afin que l’alerte de journal corresponde aux critères, vous êtes informé.

Lorsque vous recevez une alerte, utilisez les étapes de la section suivante pour résoudre les problèmes à l’origine d’une utilisation plus importante que prévu.

## <a name="next-steps"></a>Étapes suivantes
* Consultez [Recherche de données à l’aide de recherches de journal](log-analytics-queries.md) pour apprendre à utiliser le langage de recherche. Vous pouvez utiliser des requêtes de recherche pour effectuer des analyses supplémentaires sur les données d’utilisation.
* Utilisez les étapes décrites dans [Création d’une alerte de journal](../monitoring-and-diagnostics/alert-metric.md) pour être averti lorsqu’un critère de recherche est rempli.
* Utilisez le [ciblage de solution](../azure-monitor/insights/solution-targeting.md) pour collecter des données des groupes d’ordinateurs requis uniquement.
* Pour configurer une règle efficace de collecte d’événements de sécurité, passez en revue [Stratégie de filtrage de Azure Security Center](../security-center/security-center-enable-data-collection.md).
* Modifier la [configuration du compteur de performances](log-analytics-data-sources-performance-counters.md).
* Pour modifier vos paramètres de collecte d’événements, consultez [Configuration du journal des événements](log-analytics-data-sources-windows-events.md).
* Pour modifier vos paramètres de collecte de messages syslog, consultez [Configuration syslog](log-analytics-data-sources-syslog.md).
