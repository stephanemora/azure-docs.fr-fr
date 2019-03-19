---
title: Script PowerShell de création d’une ressource Application Insights | Microsoft Docs
description: Automatisez la création des ressources Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 91790f372dce4322d316b42c4bfa7ad36625c91d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315569"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>Script PowerShell de création d’une ressource Application Insights

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Lorsque vous voulez surveiller une nouvelle application, ou une nouvelle version d’une application, avec [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), vous définissez une nouvelle ressource dans Microsoft Azure. Cette ressource est l’endroit où les données de télémétrie de votre application sont analysées et affichées. 

Vous pouvez automatiser la création d’une nouvelle ressource à l’aide de PowerShell.

Par exemple, si vous développez une application pour appareil mobile, il est probable qu’à tout moment, plusieurs versions publiées de votre application soient utilisées par vos clients. Vous ne voulez pas que les résultats de télémétrie de différentes versions soient mélangés. Par conséquent, vous obtenez votre processus de génération pour créer une nouvelle ressource pour chaque build.

> [!NOTE]
> Si vous souhaitez créer un ensemble de ressources en même temps, vous pouvez [créer les ressources à l’aide d’un modèle Azure](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Script de création d’une ressource Application Insights
Consultez les spécifications d’applet de commande appropriées :

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*Script PowerShell*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Que faire avec l’iKey ?
Chaque ressource est identifiée par sa clé d’instrumentation (iKey). L’iKey est une sortie du script de création de ressources. Votre script de génération doit fournir l’iKey au kit de développement logiciel (SDK) Application Insights intégrée à votre application.

Il existe deux façons de mettre l’iKey à disposition du kit de développement logiciel (SDK) :

* Dans [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Ou dans le [code d’initialisation](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Voir aussi
* [Créer des ressources Application Insights et de test Web  templates à partir de modèles (en anglais)](powershell.md)
* [Configurer la surveillance de diagnostics Azure avec PowerShell (en anglais](powershell-azure-diagnostics.md) 
* [Définition d’alertes à l’aide de PowerShell](powershell-alerts.md)

