---
title: 'Démarrage rapide : Configurer des journaux de flux de groupe de sécurité réseau à l’aide d’un modèle Azure Resource Manager'
description: Cet article explique comment activer des journaux de flux de groupe de sécurité réseau programmatiquement avec un modèle Azure Resource Manager (modèle ARM) et Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042757"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Démarrage rapide : Configurer des journaux de flux de groupe de sécurité réseau à l’aide d’un modèle ARM

Dans ce guide de démarrage rapide, vous activez des [journaux de flux de groupe de sécurité réseau](network-watcher-nsg-flow-logging-overview.md) (journaux de flux NSG) avec un modèle [Azure Resource Manager](../azure-resource-manager/management/overview.md) (modèle ARM) et Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Nous commençons par fournir une vue d’ensemble des propriétés de l’objet de journal de flux de groupe de sécurité réseau, suivie de quelques exemples de modèles. Ensuite, nous déployons le modèle à l’aide d’une instance PowerShell locale.

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Plusieurs ressources sont définies dans le modèle :

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Objet journaux de flux NSG

L’objet de journaux de flux NSG ainsi que tous les paramètres sont indiqués ci-après. Pour obtenir une présentation complète des propriétés, consultez [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
        "enabled": "boolean",
        "workspaceResourceId": "string",
        "trafficAnalyticsInterval": "integer"
      },
      "retentionPolicy": {
        "days": "integer",
        "enabled": "boolean"
      },
      "format": {
        "type": "string",
        "version": "integer"
      }
    }
  }
}
```

Pour créer une ressource `Microsoft.Network/networkWatchers/flowLogs`, ajoutez le code JSON ci-dessus à la section des ressources de votre modèle.

## <a name="creating-your-template"></a>Création de votre modèle

Si vous utilisez des modèles ARM pour la première fois, vous pouvez en savoir plus à leur sujet en suivant les liens ci-dessous.

- [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Tutoriel : Créer et déployer votre premier modèle Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

L’exemple de modèle complet suivant est la version la plus simple, avec des paramètres minimaux transmis pour configurer les journaux de flux NSG. Pour voir d’autres exemples, consultez ce [guide pratique](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Exemple**  : Le modèle ci-dessous active les journaux de flux NSG sur un groupe de sécurité réseau cible et les stocke dans un compte de stockage donné.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {},
        "retentionPolicy": {},
        "format": {}
      }
    }
  ]
}
```

> [!NOTE]
> - Le nom de la ressource a le format _ressource parente_ressource enfant_. Ici, la ressource parente est l’instance de Network Watcher régionale (format : NetworkWatcher_NomRégion. Exemple : NetworkWatcher_centraluseuap)
> - `targetResourceId` est l’ID de ressource du groupe de sécurité réseau cible.
> - `storageId` est l’ID de ressource du compte de stockage de destination.

## <a name="deploy-the-template"></a>Déployer le modèle

Ce tutoriel part du principe que vous disposez d’un groupe de ressources et d’un NSG sur lequel vous pouvez activer la journalisation de flux.
Vous pouvez enregistrer les exemples de modèles ci-dessus localement en tant que `azuredeploy.json`. Mettez à jour les valeurs de propriété afin qu’elles pointent vers des ressources valides dans votre abonnement.

Pour déployer le modèle, exécutez la commande suivante dans PowerShell.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Les commandes ci-dessus déploient une ressource dans le groupe de ressources NetworkWatcherRG et non le groupe de ressources contenant le groupe de sécurité réseau

## <a name="validate-the-deployment"></a>Valider le déploiement

Il existe deux façons de vérifier si votre déploiement a réussi. Dans votre console PowerShell, vérifiez que `ProvisioningState` affiche `Succeeded`. Vous pouvez aussi vérifier vos modifications dans la [page du portail dédiée aux journaux de flux NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs). Si vous rencontrez des problèmes lors du déploiement, consultez [Résolution des erreurs courantes dans un déploiement Azure avec Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Azure permet la suppression des ressources via le mode de déploiement `Complete`. Pour supprimer une ressource de journaux de flux, spécifiez un déploiement en mode `Complete` sans inclure la ressource à supprimer. Découvrez-en plus sur le [mode de déploiement Complete](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Vous pouvez également désactiver un journal de flux de groupe de sécurité réseau à partir du portail Azure en procédant de la manière décrite ci-dessous :

1. Se connecter au portail Azure
1. En haut à gauche du portail, sélectionnez **Tous les services**. Dans la zone **Filtre** , entrez _Network Watcher_. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
1. Sous **Journaux** , sélectionnez **Journaux de flux NSG**.
1. Dans la liste des groupes de sécurité réseau, sélectionnez celui pour lequel vous souhaitez désactiver les journaux de flux.
1. Sous **Paramètres des journaux de flux** , définissez l’état des journaux de flux sur **Désactivé**.
1. Faites défiler vers le bas, puis sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez activé les journaux de flux NSG. Découvrez maintenant comment visualiser vos données de flux NSG avec les outils suivants :

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Outils open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure Traffic Analytics](traffic-analytics.md)
