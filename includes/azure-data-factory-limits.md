---
title: Fichier Include
description: Fichier Include
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 5/30/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: ac0747dcff4e74363a58dc9aaf6da4dbd4c6a1c7
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427579"
---
Azure Data Factory est un service partagé qui a les limites par défaut suivantes dans la place pour s’assurer que les abonnements clients sont protégés contre les charges de travail entre eux. Pour augmenter les limites jusqu'à la limite maximale pour votre abonnement, contactez le support technique.

### <a name="version-2"></a>version 2

| Ressource | Limite par défaut | Limite maximale |
| -------- | ------------- | ------------- |
| Fabriques de données d’un abonnement Azure | 50 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre total d’entités, telles que les pipelines, les jeux de données, les déclencheurs, les services liés et les runtimes d’intégration, au sein d’une fabrique de données | 5 000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre total de cœurs de processeur pour les Runtimes d’intégration Azure-SSIS sous un même abonnement | 256 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Exécutions de pipeline simultanées par data factory, qui est partagé entre tous les pipelines dans la fabrique | 10 000  | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Les exécutions d’activités externes simultanées par abonnement par [région du Runtime d’intégration Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Activités externes sont gérées sur le runtime d’intégration, mais s’exécutent sur les services liés, y compris Databricks, procédure stockée, HDInsights et autres.</small> | 3000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Les exécutions d’activités de Pipeline simultanées par abonnement par [région du Runtime d’intégration Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location) <br><small>Activités de pipeline s’exécutent sur le runtime d’intégration, y compris la recherche, GetMetadata et suppriment. </small>| 1 000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Simultanées de création d’opérations par abonnement par [région du Runtime d’intégration Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)<br><small>Y compris de tester la connexion, liste des dossiers de parcourir et liste de tables, afficher un aperçu des données. | 200 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unités de l’intégration de données simultanées<sup>1</sup> consommation par abonnement par [région du Runtime d’intégration Azure](../articles/data-factory/concepts-integration-runtime.md#integration-runtime-location)| Groupe de régions de 1<sup>2</sup>: 6000<br>Groupe de régions de 2<sup>2</sup>: 3000<br>Groupe de régions de 3<sup>2</sup>: 1 500 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Activités maximales par pipeline, qui inclut des activités internes pour les conteneurs | 40 | 40 |
| Nombre maximal de runtimes d’intégration lié qui peuvent être créés par rapport à un runtime d’intégration auto-hébergé unique | 100 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre maximal de paramètres par pipeline | 50 | 50 |
| Éléments ForEach | 100 000 | 100 000 |
| Parallélisme ForEach | 20 | 50 |
| Caractères par expression | 8 192 | 8 192 |
| Intervalle de déclencheur de fenêtre bascule minimale | 15 min | 15 min |
| Délai d’attente maximal pour l’activité du pipeline s’exécute | 7 jours | 7 jours |
| Octets par objet pour les objets pipeline<sup>3</sup> | 200 Ko | 200 Ko |
| Octets par objet pour le jeu de données et les objets de service lié<sup>3</sup> | 100 Ko | 2 000 KO |
| Unités d’intégration de données<sup>1</sup> par exécution d’activité de copie | 256 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Écrire des appels d’API | 2 500/h<br/><br/> Cette limite est imposée par Azure Resource Manager, et non par Azure Data Factory. | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Lire des appels d’API | 12 500/h<br/><br/> Cette limite est imposée par Azure Resource Manager, et non par Azure Data Factory. | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Surveillance des requêtes par minute | 1 000 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Opérations CRUD d’entité par minute | 50 | [Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |

<sup>1</sup> l’unité de l’intégration de données (DIU) est utilisée dans une opération de copie de cloud-à-cloud, en savoir plus à partir de [unités d’intégration de données (version 2)](../articles/data-factory/copy-activity-performance.md#data-integration-units). Pour plus d’informations sur la facturation, consultez [tarification d’Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

<sup>2</sup> [Runtime d’intégration azure](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) est [globalement disponible](https://azure.microsoft.com/global-infrastructure/services/) pour assurer la conformité des données, l’efficacité et réseau réduit les coûts de sortie. 

| Groupe de régions | Régions | 
| -------- | ------ |
| Groupe de régions de 1 | Centre des États-Unis, est des États-Unis, est des États-Unis 2, Europe du Nord, Europe de l’ouest, ouest des États-Unis, ouest des États-Unis 2 |
| Groupe de régions de 2 | Est de l’Australie, Sud-est de l’Australie, sud du Brésil, Inde-centre, est du Japon, Northcentral des États-Unis, centre-sud des États-Unis, Asie du Sud-est, ouest des États-Unis |
| Groupe de régions de 3 | Centre du Canada, Asie de l’est, France centre, Corée centre, sud du Royaume-Uni |

<sup>3</sup> pipeline, jeu de données et les objets de service lié représentent un regroupement logique de votre charge de travail. Limites de ces objets ne sont pas liées à la quantité de données vous pouvez déplacer et traiter avec Azure Data Factory. Data Factory est conçu pour évoluer à gérer des pétaoctets de données.

### <a name="version-1"></a>version 1

| **Ressource** | **Limite par défaut** | **Limite maximale** |
| --- | --- | --- |
| Fabriques de données d’un abonnement Azure |50 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Pipelines dans une fabrique de données |2 500 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Jeux de données au sein d’une fabrique de données |5 000 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Tranches simultanées par jeu de données |10 |10 |
| Octets par objet pour les objets pipeline<sup>1</sup> |200 Ko |200 Ko |
| Octets par objet pour les données valeur et les objets de service liés<sup>1</sup> |100 Ko |2 000 KO |
| Cœurs de cluster de la demande Azure HDInsight dans un abonnement<sup>2</sup> |60 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Unités de déplacement de données par l’exécution d’activité de copie cloud<sup>3</sup> |32 |[Contacter le support](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). |
| Nombre de nouvelles tentatives pour les exécutions d’activités de pipeline |1 000 |MaxInt (32 bits) |

<sup>1</sup> pipeline, jeu de données et les objets de service lié représentent un regroupement logique de votre charge de travail. Limites de ces objets ne sont pas liées à la quantité de données vous pouvez déplacer et traiter avec Azure Data Factory. Data Factory est conçu pour évoluer à gérer des pétaoctets de données.

<sup>2</sup> Les cœurs HDInsight à la demande sont alloués en dehors de l’abonnement qui contient la fabrique de données. Par conséquent, la limite précédente est la limite de cœurs d’appliquée à la fabrique de données pour les cœurs de HDInsight à la demande. Il est différent de la limite qui est associé à votre abonnement Azure.

<sup>3</sup> l’unité de déplacement de données cloud (DMU) pour la version 1 est utilisée dans une opération de copie de cloud-à-cloud, en savoir plus à partir de [unités de déplacement de données (version 1) Cloud](../articles/data-factory/v1/data-factory-copy-activity-performance.md#cloud-data-movement-units). Pour plus d’informations sur la facturation, consultez [tarification d’Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/).

| **Ressource** | **Limite inférieure par défaut** | **Limite minimale** |
| --- | --- | --- |
| Intervalle de planification |15 minutes |15 minutes |
| Intervalle entre les nouvelles tentatives |1 seconde |1 seconde |
| Délai d’expiration des nouvelles tentatives |1 seconde |1 seconde |

#### <a name="web-service-call-limits"></a>Limites d’appels du service web
Azure Resource Manager fixe des limites aux appels d’API. Vous pouvez effectuer des appels d’API à une fréquence comprise dans les [limites d’API d’Azure Resource Manager](../articles/azure-subscription-service-limits.md#resource-group-limits).
