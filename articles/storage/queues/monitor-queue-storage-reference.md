---
title: Informations de référence sur les données de supervision du service Stockage File d’attente Azure
description: Informations de référence sur les journaux et les métriques pour la supervision des données à partir du service Stockage File d’attente Azure.
author: normesta
services: azure-monitor
ms.author: normesta
ms.date: 04/20/2021
ms.topic: reference
ms.service: azure-monitor
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 506f5a46688f597b8ac5db341c5bbe5eb5fb67c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763142"
---
# <a name="azure-queue-storage-monitoring-data-reference"></a>Informations de référence sur les données de supervision du service Stockage File d’attente Azure

Consultez [Supervision du stockage Azure](monitor-queue-storage.md) pour plus d’informations sur la collecte et l’analyse des données de supervision du stockage Azure.

## <a name="metrics"></a>Mesures

Les tableaux suivants répertorient les métriques de plateforme collectées pour le stockage Azure.

### <a name="capacity-metrics"></a>Métriques de capacité

Les valeurs de métriques de capacité sont actualisées quotidiennement (jusqu’à 24 heures). Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de mesures sont présentées. Le fragment de temps pris en charge pour toutes les mesures de capacité est d’une heure (PT1H).

Stockage Azure fournit les mesures de capacité suivantes dans Azure Monitor.

#### <a name="account-level-capacity-metrics"></a>Métriques de capacité au niveau du compte

[!INCLUDE [Account-level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="queue-storage-metrics"></a>Métriques du Stockage File d’attente

Ce tableau montre des [métriques du Stockage File d’attente](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices).

| Métrique | Description |
| ------------------- | ----------------- |
| **QueueCapacity** | Quantité de stockage de files d’attente utilisée par le compte de stockage. <br><br> Unité : `Bytes` <br> Type d’agrégation : `Average` <br> Exemple de valeur : `1024` |
| **QueueCount** | Nombre de files d’attente dans le compte de stockage. <br><br> Unité : `Count` <br> Type d’agrégation : `Average` <br> Exemple de valeur : `1024` |
| **QueueMessageCount** | Nombre de messages de file d’attente non expirés dans le compte de stockage. <br><br> Unité : `Count` <br> Type d’agrégation : `Average` <br> Exemple de valeur : `1024` |

### <a name="transaction-metrics"></a>Métriques de transaction

Des métriques de transaction sont émises lors de chacune des requêtes adressées à un compte de stockage entre Stockage Azure et Azure Monitor. En l'absence d'activité sur votre compte de stockage, il n'y aura pas de données sur les métriques de transaction au cours la période. Toutes les métriques de transaction sont disponibles au niveau du compte et du service Stockage File d’attente. Le fragment de temps définit l’intervalle de temps auquel des valeurs de mesures sont présentées. Les fragments de temps pris en charge pour toutes les mesures de transaction sont PT1H et PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensions de mesures

Stockage Azure prend en charge les dimensions suivantes pour les mesures dans Azure Monitor.

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

## <a name="resource-logs-preview"></a>Journaux de ressources (préversion)

> [!NOTE]
> Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Cette préversion active les journaux pour les objets blob (y compris Azure Data Lake Storage Gen2), les fichiers, les files d’attente, les tables, les comptes de stockage Premium dans les comptes de stockage à usage général v1 et à usage général v2. Les comptes de stockage classiques ne sont pas pris en charge.

Le tableau suivant liste les propriétés des journaux de ressources de stockage Azure lorsqu’elles sont collectées dans les journaux Azure Monitor ou dans le Stockage Azure. Ces propriétés décrivent l’opération, le service et le type d’autorisation qui ont servi à effectuer l’opération.

### <a name="fields-that-describe-the-operation"></a>Champs qui décrivent l’opération

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Champs qui décrivent comment l’opération a été authentifiée

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Champs qui décrivent le service

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Voir aussi

- Pour obtenir une description de la supervision du Stockage File d’attente Azure, consultez [Supervision du service Stockage File d’attente Azure](monitor-queue-storage.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
