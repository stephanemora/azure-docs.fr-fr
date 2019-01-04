---
title: Fichier Include
description: Fichier Include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 12/12/2018
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 856d702bcf1f566d0cc455cdd2676e6c2e23d179
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53326576"
---
Data Factory est un service mutualisé qui possède, par défaut, les limites suivantes pour garantir la protection des abonnements clients contre les autres charges de travail. La plupart des limites de votre abonnement peuvent être facilement repoussées jusqu’à la limite maximale en contactant le support.

### <a name="version-2"></a>version 2

| Ressource | Limite par défaut | Limite maximale |
| -------- | ------------- | ------------- |
| Fabriques de données d’un abonnement Azure | 50 | [Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Nombre total d’entités (pipeline, jeux de données, déclencheurs, services liés, runtimes d’intégration) dans une fabrique de données | 5 000 | [Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Nombre total de cœurs de processeur pour les runtimes d’intégration Azure-SSIS d’un même abonnement | 128 | [Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Exécutions de pipeline simultanées par fabrique de données (partagée entre tous les pipelines en usine) | 10 000  | [Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Activités maximales par pipeline (inclut les activités internes relatives aux conteneurs) | 40 | 40 |
| Paramètres maximaux par pipeline | 50 | 50 |
| Éléments ForEach | 100 000 | 100 000 |
| Parallélisme ForEach | 20 | 50 |
| Caractères par expression | 8 192 | 8 192 |
| Intervalle minimal de déclencheur de fenêtre bascule | 15 min | 15 min |
| Délai d’expiration maximal pour les exécutions d’activité de pipeline | 7 jours | 7 jours |
| Octets par objet pour les objets pipeline <sup>1</sup> | 200 Ko | 200 Ko |
| Octets par objet pour les objets jeu de données et service lié <sup>1</sup> | 100 Ko | 2 000 Ko |
| Unités d’intégration de données par exécution d’activité de copie <sup>3</sup> | 256 | [Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Écrire des appels d’API | 2 500/h<br/><br/> Cette limite est imposée par Azure Resource Manager, et non par Azure Data Factory. | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Lire des appels d’API | 12 500/h<br/><br/> Cette limite est imposée par Azure Resource Manager, et non par Azure Data Factory. | [Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Surveillance des requêtes par minute | 1 000 | [Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Opérations CRUD d’entité par minute | 50 | [Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |


### <a name="version-1"></a>version 1

| **Ressource** | **Limite par défaut** | **Limite maximale** |
| --- | --- | --- |
| Fabriques de données d’un abonnement Azure |50 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Pipelines dans une fabrique de données |2 500 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Jeux de données dans une fabrique de données |5 000 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Tranches simultanées par jeu de données |10 |10 |
| Octets par objet pour les objets pipeline <sup>1</sup> |200 Ko |200 Ko |
| Octets par objet pour les objets jeu de données et service lié <sup>1</sup> |100 Ko |2 000 Ko |
| Cœurs de cluster HDInsight à la demande d’un abonnement<sup>2</sup> |60 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Unités de déplacement de données cloud par exécution d’activité de copie <sup>3</sup> |32 |[Contacter le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) |
| Nombre de nouvelles tentatives pour les exécutions d’activités de pipeline |1 000 |MaxInt (32 bits) |

<sup>1</sup> Les objets Pipeline, DataSet et LinkedService correspondent à un regroupement logique de votre charge de travail. Les limites de ces objets ne sont pas liées à la quantité de données que vous pouvez déplacer ou traiter à l’aide du service Azure Data Factory. Data Factory est conçu pour permettre une mise à l’échelle de plusieurs pétaoctets de données.

<sup>2</sup> Les cœurs HDInsight à la demande sont alloués en dehors de l’abonnement qui contient la fabrique de données. Par conséquent, la limite indiquée ci-dessus correspond à la limite appliquée par Data Factory pour les cœurs HDInsight à la demande et est différente de la limite associée à votre abonnement Azure.

<sup>3</sup> L’unité d’intégration de données (DIU) pour v2 ou l’unité de déplacement de données cloud (DMU) pour v1 est utilisée dans une opération de copie de cloud à cloud. C’est une mesure qui représente la puissance (combinaison de l’allocation de ressources de processeur, de mémoire et de réseau) d’une seule unité dans Data Factory. Vous pouvez obtenir un débit de copie plus élevé en utilisant plus d’unités de déplacement pour certains scénarios. Reportez-vous aux sections [Unités d’intégration de données (V2)](../articles/data-factory/copy-activity-performance.md#data-integration-units) et [Unités de déplacement de données cloud (V1)](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units) afin d’obtenir davantage de détails, et consultez la [page sur la tarification d’Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) pour connaître les implications en matière de facturation.

<sup>4</sup> Integration Runtime (IR) est l’infrastructure de calcul utilisée par Azure Data Factory pour fournir les fonctionnalités d’intégration de données suivantes entre différents environnements réseau : déplacement de données, distribution des activités aux services de calcul, exécution de packages SSIS. Pour plus d’informations, consultez [Vue d’ensemble du runtime d’intégration](../articles/data-factory/concepts-integration-runtime.md).

| **Ressource** | **Limite inférieure par défaut** | **Limite minimale** |
| --- | --- | --- |
| Intervalle de planification |15 minutes |15 minutes |
| Intervalle entre les nouvelles tentatives |1 seconde |1 seconde |
| Délai d’expiration des nouvelles tentatives |1 seconde |1 seconde |

#### <a name="web-service-call-limits"></a>Limites d’appels du service web
Azure Resource Manager fixe des limites aux appels d’API. Vous pouvez effectuer des appels d’API à une fréquence comprise dans les [limites d’API d’Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
