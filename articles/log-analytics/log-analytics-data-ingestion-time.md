---
title: Durée d’ingestion de données dans Azure Log Analytics | Microsoft Docs
description: Explique les différents facteurs qui affectent la latence lors de la collecte de données dans Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/10/2018
ms.author: bwren
ms.openlocfilehash: 0e513cc4f6a7d5d030ded807870de9eb0fdc0ed8
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38973181"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Durée d’ingestion de données dans Log Analytics
Azure Log Analytics est un service de données à grande échelle servant des milliers de clients envoyant des téraoctets de données chaque mois à un rythme croissant. Les utilisateurs se demandent souvent quel est le délai nécessaire pour que les données soient disponibles dans Log Analytics une fois qu’elles ont été collectées. Cet article explique les différents facteurs qui affectent cette latence.

## <a name="typical-latency"></a>Latence classique
La latence fait référence au délai qui s’écoule entre l’heure à laquelle les données sont créées sur le système analysé et l’heure à laquelle elles sont disponibles pour analyse dans Log Analytics. Le temps de latence habituel pour ingérer des données dans Log Analytics est compris entre 3 et 10 minutes, 95 % des données étant ingérées en moins de 7 minutes. La latence spécifique pour des données particulières dépend de divers facteurs expliqués ci-dessous.

## <a name="sla-for-log-analytics"></a>Contrat SLA pour Log Analytics
Le [contrat SLA Log Analytics](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) est un engagement juridique qui définit à quel moment Microsoft rembourse ses clients lorsque le service ne répond pas à ses objectifs. Cet engagement n’est pas basé sur les performances classiques du système, mais sur le pire des cas de figure, qui tient compte des situations graves potentielles.

## <a name="factors-affecting-latency"></a>Facteurs qui affectent la latence
Le temps total d’ingestion pour un jeu de données particulier peut être divisé en plusieurs durées (voir ci-dessous). 

- Délai de l’agent : délai nécessaire pour découvrir un événement, le collecter et l’envoyer au point d’ingestion Log Analytics sous la forme d’un enregistrement de journal. Dans la plupart des cas, ce processus est géré par un agent.
- Délai du pipeline : délai nécessaire pour que le pipeline d’ingestion traite l’enregistrement de journal. Cela inclut l’analyse des propriétés de l’événement et l’ajout éventuel des informations calculées.
- Délai d’indexation : délai nécessaire à l’ingestion d’un enregistrement de journal dans le magasin Big Data Log Analytics.

Des informations détaillées sur les différentes latences introduites dans ce processus sont décrites ci-dessous.

### <a name="agent-collection-latency"></a>Latence de collecte de l’agent
Les solutions de gestion et les agents utilisent différentes stratégies pour collecter des données d’une machine virtuelle, ce qui peut affecter la latence. Voici quelques exemples :

- Les événements Windows, événements syslog et métriques de performances sont collectés immédiatement. Les compteurs de performances Linux sont interrogés à des intervalles de 30 secondes.
- Les journaux IIS et les journaux personnalisés sont collectés une fois que leur timestamp change. Pour les journaux IIS, cela est influencé par le [calendrier de substitution configuré sur IIS](log-analytics-data-sources-iis-logs.md). 
- La solution Active Directory Replication effectue son évaluation tous les cinq jours, tandis que la solution Active Directory Assessment effectue une évaluation hebdomadaire de votre infrastructure Active Directory. L’agent collecte ces journaux uniquement lorsque l’évaluation est effectuée.

### <a name="agent-upload-frequency"></a>Fréquence de chargement de l’agent
Pour vous assurer que l’agent Log Analytics est léger, l’agent met en mémoire tampon les journaux et les charge régulièrement sur Log Analytics. La fréquence de chargement varie entre 30 secondes et 2 minutes en fonction du type de données. La plupart des données sont chargées en moins d’une minute. Les conditions du réseau peuvent affecter négativement la latence nécessaire à ces données pour atteindre le point d’ingestion Log Analytics.

### <a name="azure-logs-and-metrics"></a>Métriques et journaux Azure 
Les données de journal d’activité sont disponibles dans Log Analytics en environ 5 minutes. Les données des journaux de diagnostic et des métriques sont disponibles en 1 à 5 minutes, en fonction du service Azure. Il faut 30 à 60 secondes supplémentaires pour les journaux et 3 minutes supplémentaires pour les métriques afin que les données soient envoyées au point d’ingestion Log Analytics.

### <a name="management-solutions-collection"></a>Collecte des solutions de gestion
Certaines solutions ne collectent pas leurs données à partir d’un agent et peuvent utiliser une méthode de collecte qui introduit une latence supplémentaire. Certaines solutions collectent des données à intervalles réguliers sans tenter d’effectuer une collecte en temps quasi-réel. Voici quelques exemples :

- La solution Office 365 interroge les journaux d’activité à l’aide de l’API d’activité de gestion Office 365, qui ne fournit actuellement aucune garantie concernant une latence en temps quasi-réel.
- Les données des solutions Windows Analytics (Update Compliance par exemple) sont collectées par la solution à une fréquence quotidienne.

Reportez-vous à la documentation de chaque solution afin de déterminer sa fréquence de collecte.

### <a name="pipeline-process-time"></a>Délai du processus de pipeline
Une fois que les enregistrements de journal sont ingérés dans le pipeline Log Analytics, ils sont écrits dans un stockage temporaire pour garantir l’isolation des locataires et pour vous assurer que les données ne sont pas perdues. Ce processus ajoute généralement 5 à 15 secondes. Certaines solutions de gestion implémentent des algorithmes plus lourds pour agréger les données et dériver des insights à mesure que les données affluent. Par exemple, Network Performance Monitor agrège les données entrantes toutes les 3 minutes, en ajoutant au final une latence de 3 minutes.

### <a name="new-custom-data-types-provisioning"></a>Provisionnement des nouveaux types de données personnalisées
Quand un type de données personnalisées est créé à partir d’un [journal personnalisé](../log-analytics/log-analytics-data-sources-custom-logs.md) ou de l’[API Collecteur de données](../log-analytics/log-analytics-data-collector-api.md), le système crée un conteneur de stockage dédié. Il s’agit d’une surcharge à usage unique qui se produit uniquement à la première apparition de ce type de données.

### <a name="surge-protection"></a>Protection en cas de pics de données
La priorité numéro 1 de Log Analytics est de s’assurer que les données client ne sont pas perdues. Le système dispose donc d’une protection intégrée pour les pics de données. Cela inclut des mémoires tampons pour veiller à ce que, même sous une charge considérable, le système reste opérationnel. Sous une charge normale, ces contrôles ajoutent moins d’une minute, mais dans des conditions extrêmes et lors de défaillances, ils peuvent ajouter un délai considérable tout en garantissant la sécurité des données.

### <a name="indexing-time"></a>Délai d’indexation
Chaque plateforme Big Data propose un équilibre entre la fourniture d’analytiques et de fonctionnalités de recherche avancées et la fourniture d’un accès immédiat aux données. Azure Log Analytics vous permet d’exécuter des requêtes puissantes sur des milliards d’enregistrements et d’obtenir des résultats au bout de quelques secondes. Cela est possible, car l’infrastructure transforme les données de façon spectaculaire lors de leur ingestion et les stocke dans des structures compactes uniques. Le système met en mémoire tampon les données jusqu’à ce que suffisamment de données soient disponibles pour créer ces structures. Cette opération doit être effectuée avant que l’enregistrement de journal s’affiche dans les résultats de la recherche.

Ce processus prend environ 5 minutes avec un faible volume de données, mais moins de temps à un débit plus élevé. Cela paraît contre-intuitif, mais ce processus permet l’optimisation de la latence pour les charges de travail de production impliquant de gros volume.



## <a name="checking-ingestion-time"></a>Vérification de la durée d’ingestion
Vous pouvez utiliser la table **Pulsation** pour obtenir une estimation de la latence des données à partir d’agents. Étant donné que la pulsation est envoyée une fois par minute, la différence entre l’heure actuelle et le dernier enregistrement de pulsation est, dans l’idéal, aussi près que possible de la minute.

Utilisez la requête suivante pour répertorier les ordinateurs présentant la latence la plus élevée.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | top 50 by IngestionTime asc

 
Utilisez la requête suivante dans les grands environnements afin de résumer la latence pour différents pourcentages du nombre total d’ordinateurs.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | summarize percentiles(IngestionTime, 50,95,99)



## <a name="next-steps"></a>Étapes suivantes
* Consultez le [contrat SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) Log Analytics.

