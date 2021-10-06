---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b88ee4d53f137f98aa82ee736043943f844f36ca
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154587"
---
### <a name="app-service-application-settings-with-azure-resource-manager"></a>Paramètres d’application App Service avec Azure Resource Manager

Les paramètres d’application pour App Services peuvent être gérés et configurés à l’aide de [modèles Azure Resource Manager](../articles/azure-resource-manager/templates/syntax.md). Cette méthode s’utilise pour déployer de nouvelles ressources App Service avec Azure Resource Manager Automation, ou pour modifier les paramètres de ressources existantes.

La structure de base des paramètres d’application JSON pour un service d’application est celle-ci :

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Pour obtenir un exemple de modèle Azure Resource Manager où les paramètres d’application sont configurés pour Application Insights, consultez ce [modèle](https://github.com/Andrew-MSFT/BasicImageGallery), en particulier la section débutant à la [ligne 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatisez la création d’une ressource Application Insights et créez un lien vers votre nouvelle application App Service.

Pour créer un modèle Azure Resource Manager configuré avec tous les paramètres Application Insights par défaut, commencez le même processus que si vous souhaitiez créer une application web avec Application Insights activé. 

1. Créez une ressource App Service avec les informations d’application web souhaitées. Application Insights est activé sous l’onglet **Supervision**.

2. Sélectionnez **Vérifier + créer**, puis **Télécharger un modèle pour l’automatisation** en bas.

    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/create-web-app.png" alt-text="Capture d’écran du menu de création d’une application web App Service." :::

    Cette option génère le dernier modèle Azure Resource Manager configuré avec tous les paramètres nécessaires.
    
    :::image type="content" source="../articles/azure-monitor/app/media/azure-web-apps/arm-template.png" alt-text="Capture d’écran du modèle d’une application web App Service." border="false":::
    

Dans l’exemple ci-dessous, remplacez toutes les instances de `AppMonitoredSite` par le nom de votre site :

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enable-through-powershell"></a>Activer par le biais de PowerShell

Pour activer la supervision de l’application avec PowerShell, modifiez uniquement les paramètres de l’application sous-jacents. L’exemple ci-dessous active la supervision de l’application pour un site web appelé « AppMonitoredSite » dans le groupe de ressources « AppMonitoredRG », et configure les données à envoyer à la clé d’instrumentation « 012345678-abcd-ef01-2345-6789abcd ».

[!INCLUDE [updated-for-az](updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```