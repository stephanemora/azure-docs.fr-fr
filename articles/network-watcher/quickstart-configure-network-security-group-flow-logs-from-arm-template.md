---
title: 'Démarrage rapide : Configurer des journaux de flux de groupe de sécurité réseau à l’aide d’un modèle Azure Resource Manager'
description: Cet article explique comment activer des journaux de flux de groupe de sécurité réseau par programme à l’aide d’un modèle Azure Resource Manager et d’Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986315"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Démarrage rapide : Configurer des journaux de flux de groupe de sécurité réseau à partir d’un modèle ARM

Dans ce démarrage rapide, vous allez activer des [journaux de fluide de groupe de sécurité réseau](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) par programme à l’aide d’un modèle [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) (modèle ARM) et d’Azure PowerShell. 

Nous commençons par fournir une vue d’ensemble des propriétés de l’objet de journal de flux de groupe de sécurité réseau, suivie de quelques exemples de modèles. Ensuite, nous déployons le modèle à l’aide d’une instance PowerShell locale.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="nsg-flow-logs-object"></a>Objet de journaux de flux NSG

L'objet de journaux de flux NSG ainsi que tous les paramètres sont indiqués ci-après.
Pour obtenir une vue d’ensemble complète des propriétés, vous pouvez consulter les [informations de référence sur le modèle de journaux de flux NSG](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

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
Pour créer une ressource Microsoft.Network/networkWatchers/flowLogs, ajoutez le code JSON ci-dessus à la section des ressources de votre modèle.


## <a name="creating-your-template"></a>Création de votre modèle

Si vous utilisez des modèles Azure Resource Manager pour la première fois, vous pouvez en savoir plus à leur sujet à l’aide des liens ci-dessous.

* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Tutoriel : Créer et déployer votre premier modèle Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

L’exemple suivant de modèle complet est la version la plus simple, avec des paramètres minimaux transmis pour configurer les journaux de flux de groupe de sécurité réseau. Pour d’autres exemples, consultez ce [lien](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Exemple** : Le modèle ci-dessous active les journaux de flux NSG sur un groupe de sécurité réseau cible et les stocke dans un compte de stockage donné.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> * Le nom de la ressource a le format « Parent Resource_Child resource ». Ici, la ressource parente est l’instance de Network Watcher régionale (format : NetworkWatcher_NomRégion. Exemple : NetworkWatcher_centraluseuap)
> * targetResourceId est l’ID de ressource du groupe de sécurité réseau cible
> * storageId est l’ID de ressource du compte de stockage de destination


## <a name="deploying-your-azure-resource-manager-template"></a>Déploiement de votre modèle Azure Resource Manager

Ce tutoriel part du principe que vous disposez d’un groupe de ressources et d’un NSG sur lequel vous pouvez activer la journalisation de flux.
Vous pouvez enregistrer les exemples de modèles ci-dessus localement en tant que `azuredeploy.json`. Mettez à jour les valeurs de propriété afin qu’elles pointent vers des ressources valides dans votre abonnement.

Pour déployer le modèle, exécutez la commande suivante dans PowerShell.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Les commandes ci-dessus déploient une ressource dans le groupe de ressources NetworkWatcherRG et non le groupe de ressources contenant le groupe de sécurité réseau


## <a name="validate-the-deployment"></a>Valider le déploiement

Il existe deux façons de vérifier si votre déploiement a réussi. Votre console PowerShell doit indiquer « Succeeded » (réussite) pour « ProvisioningState ». De plus, vous pouvez consulter la [page du portail dédiée aux journaux de flux NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) pour vérifier vos modifications. Si vous rencontrez des problèmes lors du déploiement, consultez [Résolution des erreurs courantes dans un déploiement Azure avec Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Suppression de votre ressource
Azure permet la suppression des ressources via le mode de déploiement « Complet ». Pour supprimer une ressource de journaux de flux, spécifiez un déploiement en mode complet sans inclure la ressource que vous souhaitez supprimer. En savoir plus sur le [mode de déploiement complet](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

Vous pouvez également désactiver un journal de flux de groupe de sécurité réseau à partir du portail Azure en procédant de la manière décrite ci-dessous :
1. Se connecter au portail Azure
2. En haut à gauche du portail, sélectionnez **Tous les services**. Dans la zone **Filtre**, entrez *Network Watcher*. Quand la mention **Network Watcher** apparaît dans les résultats de recherche, sélectionnez-la.
3. Sous **Journaux**, sélectionnez **Journaux de flux NSG**.
4. Dans la liste des groupes de sécurité réseau, sélectionnez celui pour lequel vous souhaitez désactiver les journaux de flux.
5. Sous **Paramètres des journaux de flux**, définissez l’état du journal de flux sur **Désactivé**.
6. Faites défiler vers le bas, puis sélectionnez **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez activé les journaux de flux de groupe de sécurité réseau. Découvrez comment visualiser vos données de flux de groupe de sécurité réseau avec les outils suivants : 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Outils open source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
