---
title: Durée d’ingestion de données dans Azure Log Analytics | Microsoft Docs
description: Explique les différents facteurs qui affectent la latence lors de la collecte de données dans Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: 329472f3edee66db6b12e369ee8f944546ad4734
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900440"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Durée d’ingestion de données dans Log Analytics
Azure Log Analytics est un service de données à grande échelle dans Azure Monitor servant des milliers de clients envoyant des téraoctets de données chaque mois à un rythme croissant. Les utilisateurs se demandent souvent quel est le délai nécessaire pour que les données soient disponibles dans Log Analytics une fois qu’elles ont été collectées. Cet article explique les différents facteurs qui affectent cette latence.

## <a name="typical-latency"></a>Latence classique
La latence fait référence au délai qui s’écoule entre l’heure à laquelle les données sont créées sur le système analysé et l’heure à laquelle elles sont disponibles pour analyse dans Log Analytics. Le temps de latence habituel pour ingérer des données dans Log Analytics est compris entre 2 et 5 minutes. La latence spécifique pour des données particulières dépend de divers facteurs expliqués ci-dessous.


## <a name="factors-affecting-latency"></a>Facteurs qui affectent la latence
Le temps total d’ingestion pour un jeu de données particulier peut être divisé en plusieurs durées (voir ci-dessous). 

- Délai de l’agent : délai nécessaire pour découvrir un événement, le collecter et l’envoyer au point d’ingestion Log Analytics sous la forme d’un enregistrement de journal. Dans la plupart des cas, ce processus est géré par un agent.
- Délai du pipeline : délai nécessaire pour que le pipeline d’ingestion traite l’enregistrement de journal. Cela inclut l’analyse des propriétés de l’événement et l’ajout éventuel des informations calculées.
- Délai d’indexation : délai nécessaire à l’ingestion d’un enregistrement de journal dans le magasin Big Data Log Analytics.

Des informations détaillées sur les différentes latences introduites dans ce processus sont décrites ci-dessous.

### <a name="agent-collection-latency"></a>Latence de collecte de l’agent
Les solutions de gestion et les agents utilisent différentes stratégies pour collecter des données d’une machine virtuelle, ce qui peut affecter la latence. Voici quelques exemples :

- Les événements Windows, événements syslog et métriques de performances sont collectés immédiatement. Les compteurs de performances Linux sont interrogés à des intervalles de 30 secondes.
- Les journaux IIS et les journaux personnalisés sont collectés une fois que leur timestamp change. Pour les journaux IIS, cela est influencé par le [calendrier de substitution configuré sur IIS](../../azure-monitor/platform/data-sources-iis-logs.md). 
- La solution Active Directory Replication effectue son évaluation tous les cinq jours, tandis que la solution Active Directory Assessment effectue une évaluation hebdomadaire de votre infrastructure Active Directory. L’agent collecte ces journaux uniquement lorsque l’évaluation est effectuée.

### <a name="agent-upload-frequency"></a>Fréquence de chargement de l’agent
Pour vous assurer que l’agent Log Analytics est léger, l’agent met en mémoire tampon les journaux et les charge régulièrement sur Log Analytics. La fréquence de chargement varie entre 30 secondes et 2 minutes en fonction du type de données. La plupart des données sont chargées en moins d’une minute. Les conditions du réseau peuvent affecter négativement la latence nécessaire à ces données pour atteindre le point d’ingestion Log Analytics.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Journaux d’activité Azure, journaux de diagnostic et métriques
Les données Azure mettent un certain délai à être disponibles au point d’ingestion Log Analytics en vue de leur traitement :

- La mise à disposition des données des journaux de diagnostic prend 2 à 15 minutes, suivant le service Azure. Consultez la [requête ci-dessous](#checking-ingestion-time) pour examiner cette latence dans votre environnement.
- L’envoi des métriques de la plateforme Azure au point d’ingestion Log Analytics prend 3 minutes.
- L’envoi des données du journal d’activité au point d’ingestion Log Analytics prend entre 10 et 15 minutes.

Une fois disponibles au point d’ingestion, les données peuvent être interrogées au terme d’un délai supplémentaire de 2 à 5 minutes.


### <a name="management-solutions-collection"></a>Collecte des solutions de gestion
Certaines solutions ne collectent pas leurs données à partir d’un agent et peuvent utiliser une méthode de collecte qui introduit une latence supplémentaire. Certaines solutions collectent des données à intervalles réguliers sans tenter d’effectuer une collecte en temps quasi-réel. Voici quelques exemples :

- La solution Office 365 interroge les journaux d’activité à l’aide de l’API d’activité de gestion Office 365, qui ne fournit actuellement aucune garantie concernant une latence en temps quasi-réel.
- Les données des solutions Windows Analytics (Update Compliance par exemple) sont collectées par la solution à une fréquence quotidienne.

Reportez-vous à la documentation de chaque solution afin de déterminer sa fréquence de collecte.

### <a name="pipeline-process-time"></a>Délai du processus de pipeline
Une fois que les enregistrements de journal sont ingérés dans le pipeline Log Analytics, ils sont écrits dans un stockage temporaire pour garantir l’isolation des locataires et pour vous assurer que les données ne sont pas perdues. Ce processus ajoute généralement 5 à 15 secondes. Certaines solutions de gestion implémentent des algorithmes plus lourds pour agréger les données et dériver des insights à mesure que les données affluent. Par exemple, Network Performance Monitor agrège les données entrantes toutes les 3 minutes, en ajoutant au final une latence de 3 minutes. Un autre processus qui ajoute une latence est le processus qui gère les journaux personnalisés. Dans certains cas, ce processus peut ajouter quelques minutes de latence aux journaux qui sont collectés à partir de fichiers par l’agent.

### <a name="new-custom-data-types-provisioning"></a>Provisionnement des nouveaux types de données personnalisées
Quand un type de données personnalisées est créé à partir d’un [journal personnalisé](data-sources-custom-logs.md) ou de l’[API Collecteur de données](data-collector-api.md), le système crée un conteneur de stockage dédié. Il s’agit d’une surcharge à usage unique qui se produit uniquement à la première apparition de ce type de données.

### <a name="surge-protection"></a>Protection en cas de pics de données
La priorité numéro 1 de Log Analytics est de s’assurer que les données client ne sont pas perdues. Le système dispose donc d’une protection intégrée pour les pics de données. Cela inclut des mémoires tampons pour veiller à ce que, même sous une charge considérable, le système reste opérationnel. Sous une charge normale, ces contrôles ajoutent moins d’une minute, mais dans des conditions extrêmes et lors de défaillances, ils peuvent ajouter un délai considérable tout en garantissant la sécurité des données.

### <a name="indexing-time"></a>Délai d’indexation
Chaque plateforme Big Data propose un équilibre entre la fourniture d’analytiques et de fonctionnalités de recherche avancées et la fourniture d’un accès immédiat aux données. Azure Log Analytics vous permet d’exécuter des requêtes puissantes sur des milliards d’enregistrements et d’obtenir des résultats au bout de quelques secondes. Cela est possible, car l’infrastructure transforme les données de façon spectaculaire lors de leur ingestion et les stocke dans des structures compactes uniques. Le système met en mémoire tampon les données jusqu’à ce que suffisamment de données soient disponibles pour créer ces structures. Cette opération doit être effectuée avant que l’enregistrement de journal s’affiche dans les résultats de la recherche.

Ce processus prend environ 5 minutes avec un faible volume de données, mais moins de temps à un débit plus élevé. Cela paraît contre-intuitif, mais ce processus permet l’optimisation de la latence pour les charges de travail de production impliquant de gros volume.



## <a name="checking-ingestion-time"></a>Vérification de la durée d’ingestion
La durée d’ingestion peut varier pour différentes ressources dans différentes circonstances. Vous pouvez utiliser des requêtes de journal pour identifier un comportement spécifique de votre environnement.

### <a name="ingestion-latency-delays"></a>Délais de latence d’ingestion
Vous pouvez mesurer la latence d’un enregistrement spécifique en comparant le résultat de la fonction [ingestion_time()](/azure/kusto/query/ingestiontimefunction) au champ _TimeGenerated_. Ces données peuvent être utilisées avec différentes agrégations afin de déterminer le comportement de latence d’ingestion. Examinez certains centiles de la durée d’ingestion pour obtenir des insights sur une grande quantité de données. 

Par exemple, la requête suivante montre quels ordinateurs ont eu la durée d’ingestion la plus élevée durant cette journée : 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
Si vous souhaitez explorer au niveau du détail la durée d’ingestion pour un ordinateur spécifique sur une période donnée, utilisez la requête suivante, qui affiche également les données dans un graphe : 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
Utilisez la requête suivante pour afficher la durée d’ingestion des ordinateurs en fonction du pays où ils se trouvent, d’après leur adresse IP : 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
Différents types de données provenant de l’agent peuvent avoir des latences d’ingestion différentes ; ainsi, les requêtes précédentes pourraient être utilisées avec d’autres types. Utilisez la requête suivante pour examiner la durée d’ingestion de divers services Azure : 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Ressources qui ne répondent plus 
Dans certains cas, une ressource peut cesser d’envoyer des données. Pour savoir si une ressource envoie des données ou non, examinez son enregistrement le plus récent, identifiable par le champ _TimeGenerated_ standard.  

Utilisez la table _Heartbeat_ pour vérifier la disponibilité d’une machine virtuelle, dans la mesure où une pulsation est envoyée une fois par minute par l’agent. Utilisez la requête suivante pour lister les ordinateurs actifs qui n’ont pas signalé de pulsation récemment : 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Étapes suivantes
* Consultez le [contrat SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) Log Analytics.

