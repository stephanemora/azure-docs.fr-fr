---
title: Hébergement haute densité à l’aide de la mise à l’échelle par application - Azure App Service | Microsoft Docs
description: Hébergement haute densité sur Azure App Service
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 49b5978fd647a4667503676528120a36495021c6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730072"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hébergement haute densité sur Azure App Service à l’aide de la mise à l’échelle par application
Par défaut, vous mettez à l’échelle les applications App Service en mettant à l’échelle le [plan App Service](overview-hosting-plans.md) sur lequel elles sont exécutées. Lorsque plusieurs applications sont exécutées dans un même plan App Service, chaque instance mise à l’échelle exécute toutes les applications du plan.

Vous pouvez activer la *mise à l’échelle par application* pour chaque plan App Service. Les applications sont ainsi mises à l’échelle, indépendamment du plan App Service qui les héberge. De cette manière, un plan App Service peut passer à 10 instances, mais une application peut être définie de manière à n’en utiliser que cinq.

> [!NOTE]
> La mise à l’échelle par application est uniquement disponible pour les niveaux tarifaires **Standard**, **Premium**, **Premium V2** et **Isolé**.
>

## <a name="per-app-scaling-using-powershell"></a>Mise à l’échelle par application à l’aide de PowerShell

Créez un plan avec mise à l’échelle par application en passant le paramètre ```-PerSiteScaling $true``` dans l’applet de commande ```New-AzureRmAppServicePlan```.

```powershell
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Activez la mise à l’échelle par application avec un plan App Service existant en passant le paramètre `-PerSiteScaling $true` dans l’applet de commande ```Set-AzureRmAppServicePlan```.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Pour chaque application, configurez le nombre d’instances que celle-ci peut utiliser dans le plan App Service.

Dans l’exemple ci-dessous, l’application est limitée à deux instances quel que soit le nombre d’instances mis à l’échelle pour le plan App Service sous-jacent.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` est différent de `$newapp.MaxNumberOfWorkers`. La mise à l’échelle par application utilise `$newapp.SiteConfig.NumberOfWorkers` pour déterminer les caractéristiques de mise à l’échelle de l’application.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Mise à l’échelle par application avec Azure Resource Manager

Le modèle Azure Resource Manager ci-dessous crée les éléments suivants :

- Un plan App Service réparti sur 10 instances
- Une application configurée pour une mise à l’échelle vers un maximum de cinq instances.

Le plan App Service définit la propriété **PerSiteScaling** sur true `"perSiteScaling": true`. L’application définit le **nombre de Workers** à utiliser sur 5 `"properties": { "numberOfWorkers": "5" }`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Configuration recommandée pour l’hébergement haute densité
La fonctionnalité de mise à l’échelle par application est activée dans les régions Azure et les [environnements App Service](environment/app-service-app-service-environment-intro.md) globaux. Toutefois, la stratégie recommandée consiste à utiliser des environnements App Service pour tirer parti de leurs fonctionnalités avancées et des pools de capacité supérieure.  

Suivez ces étapes pour configurer un hébergement haute densité pour vos applications :

1. Configurez l’environnement App Service et choisissez un pool de Workers consacré au scénario d’hébergement haute densité.
2. Créez un seul plan App Service et mettez-le à l’échelle pour utiliser toute la capacité disponible sur le pool de Workers.
3. Définissez l’indicateur `PerSiteScaling` sur true dans le plan App Service.
4. Les nouvelles applications sont créées et attribuées à ce plan App Service avec la propriété **numberOfWorkers** définie sur **1**. Cela donne la densité la plus élevée possible sur ce pool de Workers.
5. Le nombre de Workers peut être configuré indépendamment par application pour accorder des ressources supplémentaires en fonction des besoins. Par exemple : 
    - Une application à haut niveau d’utilisation peut définir **numberOfWorkers** sur **3** pour accroître la capacité de traitement de cette application. 
    - Les applications à faible niveau d’utilisation définiraient **numberOfWorkers** sur **1**.

## <a name="next-steps"></a>Étapes suivantes

- [Présentation détaillée des plans Azure App Service](overview-hosting-plans.md)
- [Présentation de l'environnement App Service](environment/app-service-app-service-environment-intro.md)
