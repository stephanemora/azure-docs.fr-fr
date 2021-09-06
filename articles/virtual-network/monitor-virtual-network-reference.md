---
title: Informations de référence sur la surveillance des données du réseau virtuel
description: Documentation de référence importante nécessaire à la surveillance du réseau virtuel Azure
author: duongau
ms.topic: reference
ms.author: duau
ms.service: expressroute
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 2494c0c0cdb2b4a7b06b4cf246f70a46deb8a3a4
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113109860"
---
# <a name="monitoring-azure-virtual-network-data-reference"></a>Informations de référence sur la surveillance des données du réseau virtuel

Consultez [Surveillance du réseau virtuel Azure](monitor-virtual-network.md) pour plus d’informations sur la collecte et l’analyse des données de surveillance des réseaux virtuels Azure.

## <a name="metrics"></a>Mesures

Cette section répertorie toutes les métriques de plateforme collectées automatiquement pour le réseau virtuel Azure.  

| Type de métrique | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Réseau virtuel | [Microsoft.Network/virtualNetworks](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkvirtualnetworks) |
| interface réseau | [Microsoft.Network/networkInterfaces](../azure-monitor/essentials/metrics-supported.md#microsoftnetworknetworkinterfaces) |
| Adresse IP publique | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) |
| Passerelles NAT | [Microsoft.Network/natGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)

Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="metric-dimensions"></a>Dimensions de métrique

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Le réseau virtuel Azure a les dimensions suivantes associées à ses métriques.

### <a name="dimensions-for-nat-gateway"></a>Dimensions de la passerelle NAT

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| **Direction (Sortie - Entrée)** | Direction du flux de trafic. Les valeurs prises en charge sont Entrée et Sortie. |
| **Protocole** | Type de protocole de transport. Les valeurs prises en charge sont TCP et UDP. |

## <a name="resource-logs"></a>Journaux d’activité de ressources

Cette section répertorie les types de journaux de ressources que vous pouvez collecter pour les ressources utilisées avec un réseau virtuel Azure. 

Pour référence, consultez la liste de [tous les types de catégories de journaux de ressources pris en charge dans Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

|Type de journal de ressources | Fournisseur de ressources / espace de noms du type<br/> et lien vers des métriques individuelles |
|-------|-----|
| Un groupe de sécurité réseau | [Microsoft.Network/networksecuritygroups](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworknetworksecuritygroups) |
| Adresse IP publique | [Microsoft.Network/publicIPAddresses](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkpublicipaddresses) |

## <a name="azure-monitor-logs-tables"></a>Tables des journaux Azure Monitor

Cette section fait référence à toutes les tables Kusto des journaux d’Azure Monitor pertinentes pour le réseau virtuel Azure et qui peuvent être interrogées par Log Analytics. 

|Type de ressource | Notes |
|-------|-----|
| Réseau virtuel | [Microsoft.Network/virtualNetworks](/azure/azure-monitor/reference/tables/tables-resourcetype#virtual-networks) |
| interface réseau | [Microsoft.Network/networkInterface](/azure/azure-monitor/reference/tables/tables-resourcetype#network-interfaces) |
| Adresse IP publique | [Microsoft.Network/publicIP](/azure/azure-monitor/reference/tables/tables-resourcetype#public-ip-addresses) |

### <a name="diagnostics-tables"></a>Tables de diagnostic

**Réseau virtuel**

Le réseau virtuel Azure n’a pas de journaux de diagnostic.

## <a name="activity-log"></a>Journal d’activité

Le tableau suivant répertorie les opérations relatives au réseau virtuel Azure qui peuvent être créées dans le journal d’activité.

| Opération | Description |
|:---|:---|
| Toutes les opérations administratives | Toutes les opérations administratives, dont la création, la mise à jour et la suppression d’un réseau virtuel Azure. |
| Créer ou mettre à jour un réseau virtuel | Un réseau virtuel a été créé ou mis à jour. |
| Supprime un réseau virtuel | Un réseau virtuel a été supprimé.|

Pour plus d’informations sur le schéma des entrées du journal d’activité, consultez [Schéma du journal d’activité](../azure-monitor/essentials/activity-log-schema.md).

## <a name="see-also"></a>Voir aussi

- Pour obtenir une description de la surveillance du réseau virtuel Azure, consultez [Surveillance du réseau virtuel Azure](monitor-virtual-network.md).
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
