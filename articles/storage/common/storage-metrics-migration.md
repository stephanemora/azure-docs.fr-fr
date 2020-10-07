---
title: Passer des métriques Storage Analytics aux métriques Azure Monitor | Microsoft Docs
description: Découvrez comment effectuer la transition des métriques Storage Analytics (métriques classiques) aux métriques dans Azure Monitor.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 076f5573b599fbb83486087380174fc2da53986c
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708575"
---
# <a name="transition-to-metrics-in-azure-monitor"></a>Transition vers les métriques dans Azure Monitor

Au **31 août 2023**, les métriques Storage Analytics, également appelées *métriques classiques* seront retirées. Pour plus d’informations, consultez l’[annonce officielle](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Si vous utilisez des métriques classiques, veillez à passer aux métriques dans Azure Monitor avant cette date. Cet article vous aide à effectuer la transition. 

## <a name="steps-to-complete-the-transition"></a>Étapes pour effectuer la transition

Pour effectuer la transition vers les métriques dans Azure Monitor, nous vous recommandons l’approche suivante.

1. Découvrez les [principales différences](#key-differences-between-classic-metrics-and-metrics-in-azure-monitor) entre les métriques classiques et les métriques dans Azure Monitor. 

2. Compilez une liste des métriques classiques que vous utilisez actuellement.

3. Identifiez [les métriques dans Azure Monitor](#metrics-mapping-between-old-metrics-and-new-metrics) qui fournissent les mêmes données que les métriques que vous utilisez actuellement. 
   
4. Créer des [graphiques](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/2-viewing-blob-metrics-in-azure-portal) ou des [tableaux de bord](https://docs.microsoft.com/learn/modules/gather-metrics-blob-storage/4-using-dashboards-in-the-azure-portal) pour voir les données des métriques.

   > [!NOTE]
   > Les métriques dans Azure Monitor sont activées par défaut. Vous n’avez donc rien à faire pour commencer à capturer des métriques. Vous devez cependant créer des graphiques ou des tableaux de bord pour voir ces métriques. 
 
5. Si vous avez créé des règles d’alerte basées sur des métriques de stockage classiques, [créez des règles d’alerte](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview) basées sur les métriques dans Azure Monitor. 

6. Une fois que vous pouvez voir toutes vos métriques dans Azure Monitor, vous pouvez désactiver la journalisation classique. 

<a id="key-differences-between-classic-metrics-and-metrics-in-azure-monitor"></a>

## <a name="classic-metrics-vs-metrics-in-azure-monitor"></a>Comparaison des métriques classiques et des métriques dans Azure Monitor

Cette section décrit quelques-unes des principales différences entre ces deux plateformes de métriques.

La principale différence réside dans la façon dont les métriques sont gérées. Les métriques classiques sont gérées par le Stockage Azure, tandis que les métriques dans Azure Monitor sont gérées par Azure Monitor. Avec les métriques classiques, le Stockage Azure collecte des valeurs de métriques, les agrège, puis les stocke dans des tables qui se trouvent dans le compte de stockage. Avec les métriques dans Azure Monitor, le Stockage Azure envoie les données de métriques au back-end Azure Monitor. Azure Monitor offre une expérience de supervision unifiée, comprenant à la fois les données du portail Azure et celles qui sont ingérées. 

En ce qui concerne la prise en charge des métriques, les métriques classiques fournissent des métriques de **capacité** uniquement pour le stockage Blob Azure. Les métriques dans Azure Monitor fournissent des métriques de capacité pour le stockage d’objets blob, de tables, de fichiers, de files d’attente et Premium. Les métriques classiques fournissent des métriques de **transaction** sur le stockage d’objets blob, de tables, de fichiers Azure et de files d’attente. Les métriques dans Azure Monitor ajoutent le stockage Premium à cette liste.

Si l’activité de votre compte ne déclenche pas de métrique, les métriques classiques affichent la valeur zéro (0) pour cette métrique. Dans Azure Monitor, les métriques ignorent totalement les données, ce qui génère des rapports plus propres. Par exemple, avec les métriques classiques, si aucune erreur de délai d’attente du serveur n’est signalée, la valeur `ServerTimeoutError` dans la table de métriques est définie sur 0. Azure Monitor ne retourne aucune donnée lorsque vous interrogez la valeur de la métrique `Transactions` avec la dimension `ResponseType` égale à `ServerTimeoutError`. 

Pour en savoir plus sur les métriques dans Azure Monitor, consultez [Métriques dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

<a id="metrics-mapping-between-old-metrics-and-new-metrics"></a>

## <a name="map-classic-metrics-to-metrics-in-azure-monitor"></a>Faire correspondre les métriques classiques aux métriques dans Azure Monitor

 Utilisez ces tableaux pour identifier les métriques dans Azure Monitor qui fournissent les mêmes données que les métriques que vous utilisez actuellement. 

**Métriques de capacité**

| Métrique classique | Métrique dans Azure Monitor |
| ------------------- | ----------------- |
| `Capacity`            | `BlobCapacity` avec la dimension `BlobType` égale à `BlockBlob` ou `PageBlob` |
| `ObjectCount`        | `BlobCount` avec la dimension `BlobType` égale à `BlockBlob` ou `PageBlob` |
| `ContainerCount`      | `ContainerCount` |

> [!NOTE]
> Il existe également plusieurs nouvelles métriques de capacité qui n’étaient pas disponibles en tant que métriques classiques. Pour voir la liste complète, consultez [Métriques](../common/monitor-storage-reference.md#metrics).

**Métriques de transaction**

| Métrique classique | Métrique dans Azure Monitor |
| ------------------- | ----------------- |
| `AnonymousAuthorizationError` | Transactions avec la dimension `ResponseType` égale à `AuthorizationError` et la dimension `Authentication` égale à `Anonymous` |
| `AnonymousClientOtherError` | Transactions avec la dimension `ResponseType` égale à `ClientOtherError` et la dimension `Authentication` égale à `Anonymous` |
| `AnonymousClientTimeoutError` | Transactions avec la dimension `ResponseType` égale à `ClientTimeoutError` et la dimension `Authentication` égale à `Anonymous` |
| `AnonymousNetworkError` | Transactions avec la dimension `ResponseType` égale à `NetworkError` et la dimension `Authentication` égale à `Anonymous` |
| `AnonymousServerOtherError` | Transactions avec la dimension `ResponseType` égale à `ServerOtherError` et la dimension `Authentication` égale à `Anonymous` |
| `AnonymousServerTimeoutError` | Transactions avec la dimension `ResponseType` égale à `ServerTimeoutError` et la dimension `Authentication` égale à `Anonymous` |
| `AnonymousSuccess` | Transactions avec la dimension `ResponseType` égale à `Success` et la dimension `Authentication` égale à `Anonymous` |
| `AnonymousThrottlingError` | Transactions avec la dimension `ResponseType` égale à `ClientThrottlingError` ou `ServerBusyError` et la dimension `Authentication` égale à `Anonymous` |
| `AuthorizationError` | Transactions avec la dimension `ResponseType` égale à `AuthorizationError` |
| `Availability` | `Availability` |
| `AverageE2ELatency` | `SuccessE2ELatency` |
| `AverageServerLatency` | `SuccessServerLatency` |
| `ClientOtherError` | Transactions avec la dimension `ResponseType` égale à `ClientOtherError` |
| `ClientTimeoutError` | Transactions avec la dimension `ResponseType` égale à `ClientTimeoutError` |
| `NetworkError` | Transactions avec la dimension `ResponseType` égale à `NetworkError` |
| `PercentAuthorizationError` | Transactions avec la dimension `ResponseType` égale à `AuthorizationError` |
| `PercentClientOtherError` | Transactions avec la dimension `ResponseType` égale à `ClientOtherError` |
| `PercentNetworkError` | Transactions avec la dimension `ResponseType` égale à `NetworkError` |
| `PercentServerOtherError` | Transactions avec la dimension `ResponseType` égale à `ServerOtherError` |
| `PercentSuccess` | Transactions avec la dimension `ResponseType` égale à `Success` |
| `PercentThrottlingError` | Transactions avec la dimension `ResponseType` égale à `ClientThrottlingError` ou `ServerBusyError` |
| `PercentTimeoutError` | Transactions avec la dimension `ResponseType` égale à `ServerTimeoutError` ou `ResponseType` égal à `ClientTimeoutError` |
| `SASAuthorizationError` | Transactions avec la dimension `ResponseType` égale à `AuthorizationError` et la dimension `Authentication` égale à `SAS` |
| `SASClientOtherError` | Transactions avec la dimension `ResponseType` égale à `ClientOtherError` et la dimension `Authentication` égale à `SAS` |
| `SASClientTimeoutError` | Transactions avec la dimension `ResponseType` égale à `ClientTimeoutError` et la dimension `Authentication` égale à `SAS` |
| `SASNetworkError` | Transactions avec la dimension `ResponseType` égale à `NetworkError` et la dimension `Authentication` égale à `SAS` |
| `SASServerOtherError` | Transactions avec la dimension `ResponseType` égale à `ServerOtherError` et la dimension `Authentication` égale à `SAS` |
| `SASServerTimeoutError` | Transactions avec la dimension `ResponseType` égale à `ServerTimeoutError` et la dimension `Authentication` égale à `SAS` |
| `SASSuccess` | Transactions avec la dimension `ResponseType` égale à `Success` et la dimension `Authentication` égale à `SAS` |
| `SASThrottlingError` | Transactions avec la dimension `ResponseType` égale à `ClientThrottlingError` ou `ServerBusyError` et la dimension `Authentication` égale à `SAS` |
| `ServerOtherError` | Transactions avec la dimension `ResponseType` égale à `ServerOtherError` |
| `ServerTimeoutError` | Transactions avec la dimension `ResponseType` égale à `ServerTimeoutError` |
| `Success` | Transactions avec la dimension `ResponseType` égale à `Success` |
| `ThrottlingError` | `Transactions` avec la dimension `ResponseType` égale à `ClientThrottlingError` ou `ServerBusyError`|
| `TotalBillableRequests` | `Transactions` |
| `TotalEgress` | `Egress` |
| `TotalIngress` | `Ingress` |
| `TotalRequests` | `Transactions` |

## <a name="next-steps"></a>Étapes suivantes

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)

