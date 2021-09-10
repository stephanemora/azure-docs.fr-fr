---
title: Superviser les réseaux virtuels Azure
description: Commencez ici pour découvrir comment superviser les réseaux virtuels Azure
services: virtual-network
author: duongau
ms.author: duau
ms.service: virtual-network
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: bec4d18ebc762ecf573281341e907db4376f26b2
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297634"
---
# <a name="monitoring-azure-virtual-network"></a>Superviser le réseau virtuel Azure

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. 

Cet article décrit les données de supervision générées Réseau virtuel Azure. Le réseau virtuel Azure utilise [Azure Monitor](../azure-monitor/overview.md). Si vous n’êtes pas familiarisé avec les fonctionnalités d’Azure Monitor communes à tous les services Azure qui l’utilisent, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="monitoring-data"></a>Données de surveillance 

Réseau virtuel Azure collecte les mêmes types de données de supervision que d’autres ressources Azure, lesquelles sont décrites dans [Données de supervision de ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Pour obtenir des informations détaillées sur les métriques et les journaux de métriques créés par Réseau virtuel Azure, consultez [Informations de référence sur les données de supervision de Réseau virtuel Azure](monitor-virtual-network-reference.md).

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

Pour plus d’informations sur la création d’un paramètre de diagnostic à l’aide du portail Azure, de l’interface CLI ou de PowerShell, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md). Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. Les catégories pour *Réseau virtuel Azure* sont listées dans [Informations de référence sur les données de supervision de Réseau virtuel Azure](monitor-virtual-network-reference.md#resource-logs).

> [!IMPORTANT]
> L’activation de ces paramètres nécessite des services Azure supplémentaires (compte de stockage, hub d’événements ou Log Analytics), ce qui peut augmenter vos coûts. Pour calculer un coût estimé, consultez la rubrique [Calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator).

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques

Azure Monitor ne prend actuellement pas en charge l’analyse des métriques de *Réseau virtuel Azure* à partir de Metrics Explorer. Pour afficher les métriques de *Réseau virtuel Azure*, sélectionnez **Métriques** sous **Supervision** depuis le réseau virtuel que vous voulez analyser.

:::image type="content" source="./media/monitor-virtual-network/metrics.png" alt-text="Capture d’écran du tableau de bord des métriques pour Réseau virtuel." lightbox="./media/monitor-virtual-network/metrics-expanded.png":::

Pour obtenir la liste des métriques de plateforme collectées pour Réseau virtuel Azure, consultez [Analyse des métriques de référence de données de Réseau virtuel Azure](monitor-virtual-network-reference.md#metrics).

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Réseau virtuel Azure ne prend pas en charge les journaux de ressources.

Pour obtenir la liste des types de journaux de ressources collectés pour les ressources d’un réseau virtuel, consultez [Informations de référence sur la surveillance des données du réseau virtuel](monitor-virtual-network-reference.md#resource-logs)   

Le [journal d’activité](../azure-monitor/essentials/activity-log.md) est un type de journal de plateforme dans Azure qui fournit des insights de tous les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.  

## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/alerts/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients.

Le tableau suivant répertorie les règles d’alerte d’activité courantes et recommandées pour Réseau virtuel Azure.

| Type d’alerte | Condition | Description  |
|:---|:---|:---|
| Créer ou mettre à jour un réseau virtuel | Niveau d’événement : Tous sélectionnés, État : Tous sélectionnés, Événement initié par : Tous les services et utilisateurs | Lorsqu’un utilisateur crée ou apporte des modifications de configuration au réseau virtuel. |
| Supprimer un réseau virtuel | Niveau d’événement : Tous sélectionnés, État : Démarré | Lorsqu’un utilisateur supprime un réseau virtuel. |

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les métriques, les journaux et d’autres valeurs importantes créées par Réseau virtuel Azure, consultez [Surveillance des informations de référence sur les données de surveillance du réseau virtuel](monitor-virtual-network-reference.md).
* Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/overview.md).