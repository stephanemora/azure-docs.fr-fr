---
title: Informations de référence sur les données de supervision du service Stockage File d'attente Azure | Microsoft Docs
description: Informations de référence sur les journaux et les métriques pour la supervision des données à partir du service Stockage File d'attente Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: abf2a274b61c0f209f8ceaecdf1754536c9f53f4
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93347231"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Informations de référence sur les données de supervision du service Stockage File d'attente Azure

Consultez [Supervision du stockage Azure](monitor-queue-storage.md) pour plus d’informations sur la collecte et l’analyse des données de supervision du stockage Azure.

## <a name="metrics"></a>Mesures

Les tableaux suivants répertorient les métriques de plateforme collectées pour le stockage Azure.

### <a name="capacity-metrics"></a>Métriques de capacité

Les valeurs de métriques de capacité sont actualisées quotidiennement (jusqu’à 24 heures). Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de mesures sont présentées. Le fragment de temps pris en charge pour toutes les mesures de capacité est d’une heure (PT1H).

Stockage Azure fournit les mesures de capacité suivantes dans Azure Monitor.

#### <a name="account-level"></a>Niveau du compte

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage"></a>Stockage de files d'attente

Ce tableau montre des [métriques de Stockage File d’attente](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Métrique | Description |
| ------------------- | ----------------- |
| QueueCapacity | Quantité de stockage de files d’attente utilisée par le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| QueueCount   | Nombre de files d’attente dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| QueueMessageCount | Nombre approximatif de messages en file d’attente dans le service File d’attente du compte de stockage. <br/><br/>Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

### <a name="transaction-metrics"></a>Métriques de transaction

Des métriques de transaction sont émises lors de chacune des requêtes adressées à un compte de stockage entre Stockage Azure et Azure Monitor. En l'absence d'activité sur votre compte de stockage, il n'y aura pas de données sur les métriques de transaction au cours la période. Toutes les métriques de transaction sont disponibles au niveau du compte et du service Stockage File d'attente. Le fragment de temps définit l’intervalle de temps auquel des valeurs de mesures sont présentées. Les fragments de temps pris en charge pour toutes les mesures de transaction sont PT1H et PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensions de mesures

Stockage Azure prend en charge les dimensions suivantes pour les mesures dans Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Journaux de ressources (préversion)

> [!NOTE]
> Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Pour vous inscrire dans la préversion, consultez [cette page](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u). Cette préversion active les journaux pour les objets blob (y compris Azure Data Lake Storage Gen2), les fichiers, les files d’attente, les tables, les comptes de stockage Premium dans les comptes de stockage à usage général v1 et à usage général v2. Les comptes de stockage classiques ne sont pas pris en charge.

Le tableau suivant liste les propriétés des journaux de ressources de stockage Azure lorsqu’elles sont collectées dans les journaux Azure Monitor ou dans le Stockage Azure. Ces propriétés décrivent l’opération, le service et le type d’autorisation qui ont servi à effectuer l’opération.

### <a name="fields-that-describe-the-operation"></a>Champs qui décrivent l’opération

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Champs qui décrivent comment l’opération a été authentifiée

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Champs qui décrivent le service

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Voir également

- Consultez [Supervision du service Stockage File d'attente Azure](monitor-queue-storage.md) pour obtenir une description de la supervision du stockage Azure.
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).
