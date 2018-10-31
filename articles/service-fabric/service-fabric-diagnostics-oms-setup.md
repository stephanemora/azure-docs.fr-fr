---
title: Azure Service Fabric - Configurer le monitoring avec Log Analytics | Microsoft Docs
description: Découvrez comment configurer Log Analytics pour visualiser et analyser des événements afin de surveiller vos clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: srrengar
ms.openlocfilehash: 68374cd1675f76555ff313b42e35bdf2aed96874
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408079"
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Configurer Log Analytics pour un cluster

Nous vous recommandons d’utiliser Log Analytics pour surveiller les événements au niveau du cluster. Vous pouvez configurer un espace de travail Log Analytics à partir d’Azure Resource Manager, de PowerShell ou de la Place de marché Azure. Si vous gérez un modèle Resource Manager mis à jour de votre déploiement, utilisez le même modèle pour configurer votre environnement Log Analytics. Le déploiement via la Place de marché est plus facile si vous avez déjà déployé un cluster et activé les diagnostics. Si vous ne disposez pas d’un accès de niveau abonnement pour le compte sur lequel vous effectuez le déploiement, déployez avec PowerShell ou le modèle Resource Manager.

> [!NOTE]
> Pour configurer Log Analytics afin de surveiller votre cluster, vous avez besoin d’activer les diagnostics pour voir les événements au niveau du cluster ou de la plateforme. Reportez-vous à [Agrégation et collecte d’événements à l’aide des diagnostics Windows Azure](service-fabric-diagnostics-event-aggregation-wad.md) et [Agrégation et collection d’événements à l’aide de Linux Azure Diagnostics](service-fabric-diagnostics-event-aggregation-lad.md) pour en savoir plus

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Déployer un espace de travail Log Analytics à l’aide de la Place de marché Microsoft Azure

Si vous voulez ajouter un espace de travail Log Analytics après avoir déployé un cluster, accédez à la Place de marché Azure dans le portail, puis recherchez **Service Fabric Analytics**. Il s’agit d’une solution personnalisée pour les déploiements Service Fabric possédant des données spécifiques à Service Fabric. Dans ce processus, vous allez créer la solution (le tableau de bord pour afficher les informations) et l’espace de travail (l’agrégation des données de cluster sous-jacentes).

1. Sélectionnez **Nouveau** dans le menu de navigation gauche. 

2. Recherchez **Service Fabric Analytics**. Sélectionnez la ressource qui s’affiche.

3. Sélectionnez **Créer**.

    ![Service Fabric Analytics sur la Place de marché](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Dans la fenêtre de création Service Fabric Analytics, sélectionnez **Sélectionner un espace de travail** pour le champ **Espace de travail OMS**, puis **Créer un espace de travail**. Renseignez les entrées nécessaires. Ici, la seule exigence est que l’abonnement pour le cluster Service Fabric et celui pour l’espace de travail soient identiques. Quand vos entrées ont été validées, le déploiement de votre espace de travail commence. Ce déploiement ne prend que quelques minutes.

5. Une fois le déploiement terminé, sélectionnez une nouvelle fois **Créer** au bas de la fenêtre de création Service Fabric Analytics. Vérifiez que le nouvel espace de travail s’affiche sous **Espace de travail OMS**. Cette action ajoute la solution à l’espace de travail que vous avez créé.

Si vous utilisez Windows, passez aux étapes suivantes pour connecter Log Analytics au compte de stockage dans lequel se trouvent vos événements de cluster. 

>[!NOTE]
>Cette expérience pour les clusters Linux n’est pas encore disponible. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Connecter l’espace de travail Log Analytics à votre cluster 

1. L’espace de travail a besoin d’être connecté aux données de diagnostic provenant de votre cluster. Accédez au groupe de ressources dans lequel vous avez créé la solution Service Fabric Analytics. Sélectionnez **ServiceFabric\<nomEspaceTravail\>** et accédez à sa page de présentation. C’est là que vous pourrez modifier les paramètres de la solution et de l’espace de travail, et accéder à l’espace de travail Log Analytics.

2. Dans le menu de navigation gauche, sous **Sources de données de l’espace de travail**, sélectionnez **Journaux de comptes de stockage**.

3. Dans la page **Journaux de comptes de stockage**, sélectionnez **Ajouter** en haut pour ajouter les journaux de votre cluster à l’espace de travail.

4. Sélectionnez **Compte de stockage** pour ajouter le compte approprié créé dans votre cluster. Si vous avez utilisé le nom par défaut, le compte de stockage est nommé **sfdg\<nomGroupeRessources\>**. Vous pouvez également le confirmer avec le modèle Azure Resource Manager utilisé pour déployer votre cluster, en vérifiant la valeur utilisée pour **applicationDiagnosticsStorageAccountName**. Si le nom n’apparaît pas, faites défiler vers le bas et sélectionnez **Charger plus**. Sélectionnez le nom du compte de stockage.

5. Spécifiez le type de données. Affectez-y la valeur **Événements de Service Fabric**.

6. Vérifiez que la source a automatiquement la valeur **WADServiceFabric\*EventTable**.

7. Sélectionnez **OK** pour connecter votre espace de travail aux journaux de votre cluster.

    ![Ajouter des journaux de compte de stockage à Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Le compte apparaît maintenant dans le cadre des journaux de votre compte de stockage dans les sources de données de votre espace de travail.

Vous avez ajouté la solution Service Fabric Analytics à un espace de travail Log Analytics qui est à présent connecté à la table du journal des applications et à la plateforme de votre cluster. Vous pouvez ajouter des sources supplémentaires à l’espace de travail de la même façon.


## <a name="deploy-log-analytics-by-using-a-resource-manager-template"></a>Déployer Log Analytics avec un modèle Resource Manager

Lors du déploiement d’un cluster avec un modèle Resource Manager, celui-ci crée un espace de travail Log Analytics, y ajoute la solution Service Fabric et la configure pour lire des données provenant des tables de stockage appropriées.

Vous pouvez utiliser et modifier [cet exemple de modèle](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) pour répondre à vos besoins. Ce modèle effectue les actions suivantes :

* Crée un cluster Service Fabric à 5 nœuds
* Crée un espace de travail Log Analytics et une solution Service Fabric
* Configure l’agent Log Analytics pour collecter et envoyer 2 exemples de compteurs de performances à l’espace de travail
* Configure WAD pour collecter Service Fabric et les envoie vers les tables de stockage Azure (WADServiceFabric*EventTable)
* Configure l’espace de travail Log Analytics pour lire les événements à partir de ces tables


Vous pouvez déployer le modèle en tant que mise à niveau Resource Manager sur votre cluster à l’aide de l’API `New-AzureRmResourceGroupDeployment` dans le module AzureRM PowerShell. Voici un exemple de commande :

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager détecte que cette commande est une mise à jour d’une ressource existante. Il traite uniquement les modifications qui existent entre le modèle du déploiement existant et le nouveau modèle fourni.

## <a name="deploy-log-analytics-by-using-azure-powershell"></a>Déployer Log Analytics avec Azure PowerShell

Vous pouvez également déployer votre ressource Log Analytics avec PowerShell à l’aide de la commande `New-AzureRmOperationalInsightsWorkspace`. Pour utiliser cette méthode, vérifiez que vous avez installé [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Utilisez ce script pour créer un espace de travail Log Analytics et lui ajouter la solution Service Fabric : 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Ensuite, suivez les étapes décrites dans la section précédente pour connecter Log Analytics au compte de stockage approprié.

Vous pouvez également ajouter d’autres solutions ou apporter d’autres modifications à votre espace de travail Log Analytics avec PowerShell. Pour en savoir plus, consultez [Gérer Log Analytics à l’aide de PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Étapes suivantes
* [Déployez l’agent Log Analytics](service-fabric-diagnostics-oms-agent.md) sur vos nœuds pour collecter les compteurs de performances, ainsi que les statistiques et les journaux Docker de vos conteneurs
* Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
* [Utiliser le Concepteur de vues pour créer des vues personnalisées dans Log Analytics](../log-analytics/log-analytics-view-designer.md)
