---
title: Migration des métriques de Stockage Azure | Microsoft Docs
description: Découvrez plus d’informations sur la migration des métriques héritées vers les nouvelles métriques gérées par Azure Monitor.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: c4dc9b231668315af16c625314c737fee99d672d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-migration"></a>Migration des métriques de Stockage Azure

En adéquation avec la stratégie d’unification de l’expérience Monitor dans Azure, le Stockage Azure intègre des métriques à la plateforme Azure Monitor. À l’avenir, le service des métriques héritées sera arrêté (avec notification préalable) conformément à la stratégie Azure. Si vous utilisez des métriques de stockage héritées, vous devez effectuer la migration avant la date de fin du service pour conserver vos informations de métriques.

Cet article vous guide dans la migration des métriques héritées vers les nouvelles.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Présentation des métriques héritées gérées par le Stockage Azure

Le Stockage Azure collecte des valeurs de métrique héritées, les regroupe et les stocke dans des tables $Metric dans le même compte de stockage. Vous pouvez utiliser le portail Azure pour configurer le graphique de monitoring ainsi que les SDK de Stockage Azure afin de lire les données des tables $Metric basées sur le schéma. Pour plus d’informations, consultez [Storage Analytics](./storage-analytics.md).

Les métriques héritées fournissent des métriques de capacité seulement sur l’objet blob, et les métriques de transaction sur l’objet blob, la table, le fichier et la file d’attente.

Les métriques héritées sont conçues dans un schéma plat. Cette conception ne donne pas de valeur de métrique quand les modèles de trafic ne déclenchent pas la métrique. Par exemple, **ServerTimeoutError** est défini sur 0 dans les tables $Metric, même si vous ne recevez aucune erreur d’expiration de délai d’attente de serveur de la part du trafic en direct sur le compte de stockage.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Présentation des nouvelles métriques gérées par Azure Monitor

Pour les nouvelles métriques de stockage, le Stockage Azure envoie des données de métrique au backend Azure Monitor. Azure Monitor offre une expérience de monitoring unifiée, comprenant à la fois les données du portail et l’ingestion de données. Pour plus d’informations, consultez cet [article](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Les nouvelles métriques fournissent des métriques de capacité et des métriques de transaction sur l’objet blob, la table, le fichier, la file d’attente et le stockage Premium.

La conception multidimension est une des fonctionnalités fournies par Azure Monitor. Le Stockage Azure adopte cette conception en définissant un nouveau schéma de métrique. Pour plus d’informations sur les dimensions prises en charge sur les métriques, consultez [Métriques de Stockage Azure dans Azure Monitor](./storage-metrics-in-azure-monitor.md). La conception multidimension permet de rentabiliser la bande passante par l’ingestion, et la capacité par les métriques de stockage. Par conséquent, si votre trafic n’a pas déclenché de métriques connexes, les données de métrique associées ne sont pas générées. Par exemple, si votre trafic n’a pas déclenché d’erreur d’expiration de délai d’attente de serveur, Azure Monitor ne retourne aucune donnée quand vous interrogez la valeur de métrique **Transactions** avec la dimension **ResponseType** égale à **ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>Mappage entre les métriques héritées et les nouvelles métriques

Si vous lisez les données de métriques par programmation, vous devez adopter le nouveau schéma de métriques dans vos programmes. Pour mieux comprendre les changements, vous pouvez consulter le mappage dans le tableau suivant :

**Métriques de capacité**

| Métrique héritée | Nouvelle métrique |
| ------------------- | ----------------- |
| Capacity            | BlobCapacity avec la dimension BlobType égale à BlockBlob ou PageBlob |
| ObjectCount         | BlobCount avec la dimension BlobType égale à BlockBlob ou PageBlob |
| ContainerCount      | ContainerCount |

Les métriques suivantes sont de nouvelles offres que les métriques héritées ne prennent pas en charge :
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Métriques de transaction**

| Métrique héritée | Nouvelle métrique |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | Transactions avec la dimension ResponseType égale à AuthorizationError |
| AnonymousClientOtherError | Transactions avec la dimension ResponseType égale à ClientOtherError |
| AnonymousClientTimeoutError | Transactions avec la dimension ResponseType égale à ClientTimeoutError |
| AnonymousNetworkError | Transactions avec la dimension ResponseType égale à NetworkError |
| AnonymousServerOtherError | Transactions avec la dimension ResponseType égale à ServerOtherError |
| AnonymousServerTimeoutError | Transactions avec la dimension ResponseType égale à ServerTimeoutError |
| AnonymousSuccess | Transactions avec la dimension ResponseType égale à Success |
| AnonymousThrottlingError | Transactions avec la dimension ResponseType égale à ClientThrottlingError ou ServerBusyError |
| AuthorizationError | Transactions avec la dimension ResponseType égale à AuthorizationError |
| Disponibilité | Disponibilité |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | Transactions avec la dimension ResponseType égale à ClientOtherError |
| ClientTimeoutError | Transactions avec la dimension ResponseType égale à ClientTimeoutError |
| NetworkError | Transactions avec la dimension ResponseType égale à NetworkError |
| PercentAuthorizationError | Transactions avec la dimension ResponseType égale à AuthorizationError |
| PercentClientOtherError | Transactions avec la dimension ResponseType égale à ClientOtherError |
| PercentNetworkError | Transactions avec la dimension ResponseType égale à NetworkError |
| PercentServerOtherError | Transactions avec la dimension ResponseType égale à ServerOtherError |
| PercentSuccess | Transactions avec la dimension ResponseType égale à Success |
| PercentThrottlingError | Transactions avec la dimension ResponseType égale à ClientThrottlingError ou ServerBusyError |
| PercentTimeoutError | Transactions avec la dimension ResponseType égale à ServerTimeoutError ou ResponseType égale à ClientTimeoutError|
| SASAuthorizationError | Transactions avec la dimension ResponseType égale à AuthorizationError |
| SASClientOtherError | Transactions avec la dimension ResponseType égale à ClientOtherError |
| SASClientTimeoutError | Transactions avec la dimension ResponseType égale à ClientTimeoutError |
| SASNetworkError | Transactions avec la dimension ResponseType égale à NetworkError |
| SASServerOtherError | Transactions avec la dimension ResponseType égale à ServerOtherError |
| SASServerTimeoutError | Transactions avec la dimension ResponseType égale à ServerTimeoutError |
| SASSuccess | Transactions avec la dimension ResponseType égale à Success |
| SASThrottlingError | Transactions avec la dimension ResponseType égale à ClientThrottlingError ou ServerBusyError |
| ServerOtherError | Transactions avec la dimension ResponseType égale à ServerOtherError |
| ServerTimeoutError | Transactions avec la dimension ResponseType égale à ServerTimeoutError |
| Succès | Transactions avec la dimension ResponseType égale à Success |
| ThrottlingError | Transactions avec la dimension ResponseType égale à ClientThrottlingError ou ServerBusyError |
| TotalBillableRequests | Transactions |
| TotalEgress | Sortie |
| TotalIngress | Entrée |
| TotalRequests | Transactions |

## <a name="faq"></a>Forum Aux Questions

* Comment migrer des règles d’alerte existantes ?

R : Si vous avez créé des règles d’alerte classiques basées sur des métriques de stockage héritées, vous devez créer des règles d’alerte basées sur le nouveau schéma de métriques.

* Les nouvelles données de métrique sont-elles stockées dans le même compte de stockage par défaut ?

R : Non. Si vous avez besoin d’archiver les données de métrique dans un compte de stockage, vous pouvez utiliser [Paramètre de diagnostic dans Azure Monitor](https://azure.microsoft.com/blog/azure-monitor-multiple-diagnostic-settings/)

## <a name="next-steps"></a>Étapes suivantes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métriques de stockage dans Azure Monitor](./storage-metrics-in-azure-monitor.md)
