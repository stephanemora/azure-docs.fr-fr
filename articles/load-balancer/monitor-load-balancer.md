---
title: Surveillance d’Azure Load Balancer
description: Commencer ici pour découvrir comment surveiller l’équilibreur de charge.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: 62885e889c64c40c051f838178f9a96d4ac4a8ee
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291175"
---
# <a name="monitoring-load-balancer"></a>Surveillance de l’équilibreur de charge

Lorsque vous avez des applications critiques et des processus métier basés sur des ressources Azure, vous voulez superviser ces ressources pour connaître leur disponibilité, leurs performances et leur fonctionnement. 

Cet article décrit les données de surveillance générées par Load Balancer. L’Équilibreur de charge utilise [Azure Monitor](../azure-monitor/overview.md). Si vous n’êtes pas familiarisé avec les fonctionnalités d’Azure Monitor communes à tous les services Azure qui l’utilisent, consultez [Supervision de ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

## <a name="load-balancer-insights"></a>Insights de l’équilibreur de charge

Dans Azure, certains services disposent d’un tableau de bord de surveillance prédéfini spécial dans le portail Azure qui constitue un point de départ pour la surveillance de votre service. Ces tableaux de bord spéciaux sont appelés « insights ».

Les insights de Load Balancer fournissent :

* Vue des dépendances fonctionnelles
* Tableau de bord des métriques
* Onglet Overview
* Onglet Disponibilité des front-ends et des back-ends
* Onglet Débit de données
* Distribution des flux
* Moniteurs de connexion
* Définitions de métriques

Pour plus d’informations sur les insights de l’Équilibreur de charge, consultez [Utilisation des insights pour surveiller et configurer votre Azure Load Balancer](./load-balancer-insights.md)

## <a name="monitoring-data"></a>Données de surveillance 

L’Équilibreur de charge collecte les mêmes types de données de surveillance que d’autres ressources Azure, lesquelles sont décrites dans [Supervision des données à partir des ressources Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Pour obtenir des informations détaillées sur les métriques et les métriques de journaux créées par Load Balancer, consultez [Informations de référence sur les données de surveillance de Load Balancer](monitor-load-balancer.md).

Load Balancer fournit des données de surveillance supplémentaires via :

* [Sondes d’intégrité](./load-balancer-custom-probe-overview.md)
* [État d’intégrité des ressources](./load-balancer-standard-diagnostics.md#resource-health-status)
* [REST API](./load-balancer-query-metrics-rest-api.md)

## <a name="collection-and-routing"></a>Collecte et routage

Les métriques de plateforme et le journal d’activité sont collectés et stockés automatiquement, mais ils peuvent être acheminés vers d’autres emplacements à l’aide d’un paramètre de diagnostic.  

Les journaux de ressources ne sont pas collectés ni stockés tant que vous n’avez pas créé un paramètre de diagnostic et que vous ne les acheminez pas vers un ou plusieurs emplacements.

## <a name="creating-a-diagnostic-setting"></a>Création d’un paramètre de diagnostic

Vous pouvez créer un paramètre de diagnostic avec le portail Azure, PowerShell ou l’interface Azure CLI.


Pour obtenir des instructions générales, consultez [Créer un paramètre de diagnostic pour collecter des journaux et métriques de plateforme dans Azure](../azure-monitor/essentials/diagnostic-settings.md).

Lorsque vous créez un paramètre de diagnostic, vous spécifiez les catégories de journaux à collecter. La catégorie de Load Balancer est **AllMetrics**

### <a name="portal"></a>Portail

1. Connectez-vous au [portail Azure](https://portal.azure.com)

2. Dans la zone de recherche située en haut du portail, entrez **Équilibreur de charge**.

3. Sélectionnez **Équilibreurs de charge** dans les résultats de la recherche.

4. Sélectionnez votre équilibreur de charge. Dans cet exemple, on a utilisé **myLoadBalancer**.

5. Dans la section **Surveillance** de **myLoadBalancer**, sélectionnez **Paramètres de diagnostic**.

6. Dans **Paramètres de diagnostic**, sélectionnez **+Ajouter un paramètre de diagnostic**.

7. Entrez ou sélectionnez les informations suivantes dans **Paramètre de diagnostic**.

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom du paramètre de diagnostic | Entrez un nom pour le paramètre de diagnostic. |
    | **Détails de la catégorie** |   |
    | Métrique | Sélectionnez **AllMetrics**. |

8. Sélectionnez les **Détails de la destination**. Voici certaines options de destination :
    * **Envoyer à Log Analytics**
        * Sélectionnez l’**Abonnement** et l’**Espace de travail Log Analytics**.
    * **Archiver dans un compte de stockage**
        * Sélectionnez l’**Abonnement** et le **Compte de stockage**.
    * **Diffuser vers un hub d’événements**
        * Sélectionnez l’**Abonnement**, l’**Espace de noms Event hub**, le **Nom de l’Event hub (facultatif)** et le **Nom de la stratégie de l’Event hub**

9. Sélectionnez **Enregistrer**.

### <a name="powershell"></a>PowerShell

Connectez-vous à Azure PowerShell :

```azurepowershell
Connect-AzAccount 
```

#### <a name="log-analytics-workspace"></a>Espace de travail Log Analytics

Pour activer les journaux de diagnostic pour un espace de travail Log Analytics, entrez ces commandes. Remplacez les valeurs entre parenthèses par vos propres valeurs :

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the workspace in a variable. ##
$wspara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-log-analytics-workspace-name>
}
$ws = Get-AzOperationalInsightsWorkspace @wspara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -Enabled $true `
    -MetricCategory 'AllMetrics' `
    -WorkspaceId $ws.ResourceId
```

#### <a name="storage-account"></a>Compte de stockage

Pour activer les journaux de diagnostic dans un compte de stockage, entrez ces commandes. Remplacez les valeurs entre parenthèses par vos propres valeurs :

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the storage account in a variable. ##
$storpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-storage-account-name>
}
$storage = Get-AzStorageAccount @storpara
    
## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.id `
    -Name <your-diagnostic-setting-name> `
    -StorageAccountId $storage.id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```

#### <a name="event-hub"></a>Event Hub

Pour activer les journaux de diagnostic pour un espace de noms Event Hubs, entrez ces commandes. Remplacez les valeurs entre parenthèses par vos propres valeurs :

```azurepowershell
## Place the load balancer in a variable. ##
$lbpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-load-balancer-name>
}
$lb = Get-AzLoadBalancer @lbpara
    
## Place the event hub in a variable. ##
$hubpara = @{
    ResourceGroupName = <your-resource-group-name>
    Name = <your-event-hub-name>
}
$eventhub = Get-AzEventHubNamespace @hubpara

## Place the event hub authorization rule in a variable. ##    
$hubrule = @{
    ResourceGroupName = 'myResourceGroup'
    Namespace = 'myeventhub8675'
}
$eventhubrule = Get-AzEventHubAuthorizationRule @hubrule

## Enable the diagnostic setting. ##
Set-AzDiagnosticSetting `
    -ResourceId $lb.Id `
    -Name 'myDiagSetting-event'`
    -EventHubName $eventhub.Name `
    -EventHubAuthorizationRuleId $eventhubrule.Id `
    -Enabled $true `
    -MetricCategory 'AllMetrics'
```
### <a name="azure-cli"></a>Azure CLI

Connectez-vous à Azure CLI :

```azurecli
az login
```

#### <a name="log-analytics-workspace"></a>Espace de travail Log Analytics

Pour activer les journaux de diagnostic pour un espace de travail Log Analytics, entrez ces commandes. Remplacez les valeurs entre parenthèses par vos propres valeurs :

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

wsid=$(az monitor log-analytics workspace show \
    --resource-group <your-resource-group> \
    --workspace-name <your-log-analytics-workspace-name> \
    --query id \
    --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --workspace $wsid
```

#### <a name="storage-account"></a>Compte de stockage

Pour activer les journaux de diagnostic dans un compte de stockage, entrez ces commandes. Remplacez les valeurs entre parenthèses par vos propres valeurs :

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

storid=$(az storage account show \
        --name <your-storage-account-name> \
        --resource-group <your-resource-group> \
        --query id \
        --output tsv)
    
az monitor diagnostic-settings create \
    --name <your-diagnostic-setting-name> \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --storage-account $storid
```

#### <a name="event-hub"></a>Event Hub

Pour activer les journaux de diagnostic pour un espace de noms Event Hubs, entrez ces commandes. Remplacez les valeurs entre parenthèses par vos propres valeurs :

```azurecli
lbid=$(az network lb show \
    --name <your-load-balancer-name> \
    --resource-group <your-resource-group> \
    --query id \
    --output tsv)

az monitor diagnostic-settings create \
    --name myDiagSetting-event \
    --resource $lbid \
    --metrics '[{"category": "AllMetrics","enabled": true}]' \
    --event-hub-rule /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>/providers/Microsoft.EventHub/namespaces/<your-event-hub-namespace>/authorizationrules/RootManageSharedAccessKey
```

Les métriques et les journaux que vous pouvez collecter sont décrits dans les sections suivantes.

## <a name="analyzing-metrics"></a>Analyse des métriques

Vous pouvez analyser les métriques de l’Équilibreur de charge avec les métriques d’autres services Azure à l’aide de l’explorateur de métriques en ouvrant **Métriques** dans le menu **Azure Monitor**. Pour plus d’informations sur l’utilisation de cet outil, consultez [Prise en main d’Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md). 

Pour obtenir la liste des métriques de plateforme collectées pour l’Équilibreur de charge, consultez [Surveillance des métriques de référence des données de Load Balancer](monitor-load-balancer-reference.md#metrics)  

Pour référence, vous pouvez voir une liste de [toutes les métriques de ressources prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="analyzing-logs"></a>Analyse des journaux d’activité

Les données des journaux Azure Monitor sont stockées dans des tables, chacune ayant son propre ensemble de propriétés uniques.  

Le [Journal d’activité](../azure-monitor/essentials/activity-log.md) est un type de journal de plateforme qui fournit des insights dans les événements de niveau abonnement. Vous pouvez l’afficher indépendamment ou le router vers Azure Monitor Logs, où vous pouvez effectuer des requêtes bien plus complexes à l’aide de Log Analytics.  

Pour obtenir la liste des tables utilisées par les Journaux de surveillance Azure Monitor et interrogeables par Log Analytics, consultez [Informations de référence sur les données de surveillance de l’Équilibreur de charge](monitor-load-balancer-reference.md#azure-monitor-logs-tables)  

### <a name="sample-kusto-queries"></a>Exemples de requêtes Kusto

> [!NOTE]
> Il existe actuellement un problème avec les requêtes Kusto qui empêche la récupération des données à partir des journaux de l’équilibreur de charge.


## <a name="alerts"></a>Alertes

Azure Monitor vous avertit de façon proactive lorsque des conditions significatives sont détectées dans vos données de surveillance. Elles permettent d’identifier et de résoudre les problèmes affectant votre système avant que vos clients ne les remarquent. Vous pouvez définir des alertes sur des [métriques](../azure-monitor/alerts/alerts-metric-overview.md), sur des [journaux](../azure-monitor/alerts/alerts-unified-log.md) et sur le [journal d’activité](../azure-monitor/alerts/activity-log-alerts.md). Les différents types d’alertes présentent des avantages et des inconvénients

Si vous créez ou exécutez une application qui utilise l’Équilibreur de charge, les [Insights d’application Azure Monitor](../azure-monitor/overview.md#application-insights) peuvent vous proposer des types d’alertes supplémentaires.


La table suivante répertorie les règles d’alerte courantes et recommandées pour Load Balancer.

| Type d’alerte | Condition | Description  |
|:---|:---|:---|
| Règle d’équilibrage de charge indisponible en raison de machines virtuelles indisponibles | Si la disponibilité du chemin de données fractionnée par l’adresse IP du serveur frontal et le port frontend (toutes les valeurs connues et futures) est égale à zéro et l’état de la sonde d’intégrité est égal à zéro, alors déclenchez des alertes | Cette alerte détermine si la disponibilité du chemin d’accès aux données pour les règles d’équilibrage de charge configurées ne traite pas le trafic en raison de la détection par la sonde d’intégrité configurée de toutes les machines virtuelles du pool principal associé. Consultez le [guide de résolution des problèmes](load-balancer-troubleshoot.md) de l’équilibreur de charge pour identifier la cause racine éventuelle. |
| Disponibilité des machines virtuelles considérablement faible | Si l’état de la sonde d’intégrité fractionné par l’adresse IP du serveur principal et le port principal est égal au pourcentage d’exploration défini par l’utilisateur (par exemple, si 25 % sont détectés), alors déclenchez une alerte | Cette alerte détermine s’il y a moins de machines virtuelles que nécessaire pour traiter le trafic |
| Échec des connexions sortantes au point de terminaison Internet | Si le nombre de connexions SNAT filtré à l’État de la connexion = échec supérieur à zéro, alors déclenchez l’alerte | Cette alerte se déclenche lorsque les ports SNAT sont épuisés et que les machines virtuelles ne parviennent pas à établir des connexions sortantes. |
| Approche de l’épuisement SNAT | Si les ports SNAT utilisés sont supérieurs au nombre défini par l’utilisateur, alors déclenchez l’alerte | Cette alerte nécessite une configuration sortante statique où le même nombre de ports est toujours alloué. Elle se déclenche lorsqu’un pourcentage des ports alloués est utilisé. |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les métriques, les journaux et autres valeurs importantes créées par l’équilibreur de charge, consultez [Surveillance des informations de référence sur les données de surveillance de l’équilibreur de charge](monitor-load-balancer-reference.md) .
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).