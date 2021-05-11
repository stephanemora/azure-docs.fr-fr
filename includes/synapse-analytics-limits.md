---
title: Fichier include
description: Fichier include
services: synapse-analytics
author: jonburchel
ms.service: synapse-analytics
ms.topic: include
ms.date: 04/15/2021
ms.author: jburchel
ms.custom: include file
ms.openlocfilehash: 425ab7d55d8f87f927c511bdb27477049e8fdd3e
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108290383"
---
Azure Synapse Analytics a les limites par défaut suivantes pour garantir que chaque abonnement du client est protégé des charges de travail de ses autres abonnements. Pour augmenter les limites jusqu’à la limite maximale pour votre abonnement, contactez le support.

### <a name="synapse-workspace-limits"></a>Limites d’un espace de travail Azure Synapse Analytics

| Ressource | Limite par défaut | Limite maximale | 
| -------- | ------------- | ------------- |
| Espaces de travail Azure Synapse Analytics dans un abonnement Azure | 20 | 20 |

### <a name="synapse-pipeline-limits"></a>Limites du pipeline Azure Synapse Analytics

| Ressource | Limite par défaut | Limite maximale |
| -------- | ------------- | ------------- |
| Pipelines Azure Synapse Analytics dans un espace de travail Azure Synapse Analytics | 800 | 800 |
| Nombre total d’entités, comme les pipelines, les jeux de données, les déclencheurs, les services liés, les points de terminaison privés et les runtimes d’intégration, dans un espace de travail | 5 000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre total de cœurs d’UC pour les runtimes d’intégration Azure-SSIS d’un même espace de travail | 256 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Exécutions simultanées de pipeline par espace de travail qui sont partagées entre tous les pipelines de l’espace de travail | 10 000  | 10 000 |
| Exécutions simultanées d’activités externes par espace de travail et par [région Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Les activités externes sont gérées sur le runtime d’intégration, mais s’exécutent sur des services liés, notamment Databricks, une procédure stockée, HDInsight, le web et autres. Cette limite ne s’applique pas au runtime d’intégration auto-hébergé.</small> | 3 000 | 3 000 |
| Exécutions simultanées d’activités de pipeline par espace de travail et par [région Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location) <br><small>Les activités de pipeline s’exécutent sur le runtime d’intégration, notamment Lookup, GetMetadata et Delete. Cette limite ne s’applique pas au runtime d’intégration auto-hébergé.</small> | 1 000 | 1 000                                                        |
| Opérations de création simultanées par espace de travail et par [région Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-ir-location)<br><small>Notamment tester la connexion, parcourir la liste des dossiers des tables, afficher un aperçu des données. Cette limite ne s’applique pas au runtime d’intégration auto-hébergé.</small> | 200 | 200                                                          |
| Consommation simultanée d’unités d’intégration de données<sup>1</sup> par espace de travail et par [région Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Groupe de régions 1<sup>2</sup> : 6 000 / 750<br>Groupe de régions 2<sup>2</sup> : 3 000<br>Groupe de régions 3<sup>2</sup> : 1 500 | Groupe de régions 1<sup>2</sup> : 6 000 / 750<br/>Groupe de régions 2<sup>2</sup> : 3 000<br/>Groupe de régions 3<sup>2</sup> : 1 500 |
| Activités maximales par pipeline, notamment les activités internes relatives aux conteneurs | 40 | 40 |
| Nombre maximal de runtime d'intégration liés pouvant être créés pour un même runtime d'intégration auto-hébergé | 100 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre maximal de paramètres par pipeline | 50 | 50 |
| Éléments ForEach | 100 000 | 100 000 |
| Parallélisme ForEach | 20 | 50 |
| Nombre maximal d’exécutions en file d’attente par pipeline | 100 | 100 |
| Caractères par expression | 8 192 | 8 192 |
| Intervalle minimal de déclencheur de fenêtre bascule | 15 min | 15 min |
| Délai d’expiration maximal pour les exécutions d’activité de pipeline | 7 jours | 7 jours |
| Octets par objet pour les objets pipeline <sup>3</sup> | 200 Ko | 200 Ko |
| Octets par objet pour les objets jeu de données et service lié <sup>3</sup> | 100 Ko | 2 000 Ko |
| Octets par charge utile pour chaque exécution d’activité<sup>4</sup> | 896 Ko | 896 Ko |
| Unités d’intégration de données par exécution d’activité de copie <sup>1</sup> | 256 | 256 |
| Écrire des appels d’API | 1 200/h | 1 200/h<br/><br/> Cette limite est imposée par Azure Resource Manager, et non par Azure Synapse Analytics. |
| Lire des appels d’API | 12 500/h | 12 500/h<br/><br/> Cette limite est imposée par Azure Resource Manager, et non par Azure Synapse Analytics. |
| Surveillance des requêtes par minute | 1 000 | 1 000 |
| Durée maximale d’une session de débogage de flux de données | 8 heures | 8 heures |
| Nombre de flux de données simultanés par runtime d’intégration | 50 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre de sessions simultanées de débogage de flux de données par utilisateur et par espace de travail | 3 | 3 |
| Limite de durée de vie de flux de données avec Azure IR | 4 heures |  4 heures |
| Limite de taille des entités de métadonnées dans un espace de travail | 2 Go | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> L’unité d’intégration de données (DIU) est utilisée dans une opération de copie de cloud à cloud, pour en savoir plus consultez [Unités d’intégration de données (version 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Pour plus d’informations sur la facturation, consultez [Tarification Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/).

<sup>2</sup> [Azure Integration Runtime](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) est [disponible à l’échelle Mondiale](https://azure.microsoft.com/global-infrastructure/services/) pour assurer la conformité des données, l’efficacité et des coûts de sortie réseau réduits. 

| Groupe de régions | Régions |
| -------- | ------ |
| Groupe de régions 1 | USA Centre, USA Est, USA Est 2, Europe Nord, Europe Ouest, USA Ouest, USA Ouest 2 |
| Groupe de régions 2 | Australie Est, Australie Sud-Est, Brésil Sud, Inde Centre, Japon Est, USA Centre Nord, USA Centre Sud, Asie Sud-Est, USA Centre-Ouest |
| Groupe de régions 3 | Autres régions |

<sup>3</sup> Les objets pipeline, jeu de données et service lié représentent un regroupement logique de votre charge de travail. Les limites de ces objets ne sont pas liées à la quantité de données que vous pouvez déplacer ou traiter à l’aide d’Azure Synapse Analytics. Azure Synapse Analytics est conçu pour mettre à l’échelle et traiter plusieurs pétaoctets de données.

<sup>4</sup> La charge utile pour chaque exécution d’activité comprend la configuration de l’activité, le ou les jeux de données associés et les configurations du ou des services liés le cas échéant, et une petite partie des propriétés système générées par type d’activité. La limite de cette taille de charge utile n’est pas liée à la quantité de données que vous pouvez déplacer ou traiter avec Azure Synapse Analytics. Découvrez les [symptômes et recommandations](../articles/data-factory/data-factory-troubleshoot-guide.md#payload-is-too-large) si vous atteignez cette limite.

### <a name="dedicated-sql-pool-limits"></a>Limites des pools SQL dédiés
Pour plus d’informations sur les limites de capacité des pools SQL dédiés dans Azure Synapse Analytics, consultez [Limites des ressources de pool SQL dédié](../articles/synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

### <a name="web-service-call-limits"></a>Limites d’appels du service web
Azure Resource Manager fixe des limites aux appels d’API. Vous pouvez effectuer des appels d’API à une fréquence comprise dans les [limites d’API d’Azure Resource Manager](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).
