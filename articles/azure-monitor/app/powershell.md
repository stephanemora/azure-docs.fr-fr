---
title: Automatiser Azure Application Insights avec PowerShell | Microsoft Docs
description: Automatisez la création et la gestion des ressources, des alertes et des tests de disponibilité dans PowerShell à l’aide d’un modèle Azure Resource Manager.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/17/2019
ms.openlocfilehash: 2f59b66bacbd0105e5a543b019a5c6646e010c48
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928515"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Gérer les ressources Application Insights à l’aide de PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Cet article explique comment automatiser la création et la mise à jour de ressources [Application Insights](../../azure-monitor/app/app-insights-overview.md) à l’aide du service de gestion des ressources Azure. Cette opération peut par exemple avoir lieu dans le cadre du processus de génération. Avec la ressource Application Insights de base, vous pouvez créer des [tests web de disponibilité](../../azure-monitor/app/monitor-web-app-availability.md), configurer [des alertes](../../azure-monitor/app/alerts.md) et un [mécanisme de tarification](pricing.md), mais aussi créer d’autres ressources Azure.

Les éléments importants pour la création de ces ressources sont les modèles JSON pour [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). La procédure de base est la suivante : téléchargez les définitions JSON des ressources existantes, paramétrez certaines valeurs telles que les noms, puis exécutez le modèle chaque fois que vous souhaitez créer une ressource. Vous pouvez regrouper plusieurs ressources pour les créer en une fois, par exemple une analyse d’application avec des tests de disponibilité, des alertes et le stockage pour l’exportation continue. Certains paramètres ont des particularités que nous aborderons ici.

## <a name="one-time-setup"></a>Installation unique
Si vous n’avez pas utilisé précédemment PowerShell avec votre abonnement Azure :

Installez le module Azure Powershell sur l’ordinateur sur lequel vous souhaitez exécuter les scripts :

1. Installez le programme [Microsoft Web Platform Installer (v5 ou version ultérieure)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Utilisez-le pour installer Microsoft Azure PowerShell.

En plus des modèles Resource Manager, il existe un ensemble complet d’[applets de commande PowerShell Application Insights](https://docs.microsoft.com/powershell/module/az.applicationinsights), qui facilite la configuration des ressources Application Insights par programmation. Les fonctionnalités activées par les applets de commande sont les suivantes :

* Créer et supprimer des ressources Application Insights
* Obtenir les listes de ressources Application Insights et leurs propriétés
* Créer et gérer une exportation continue
* Créer et gérer des clés d’application
* Définir la limite quotidienne
* Définir le plan tarifaire

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Créer des ressources Application Insights à l’aide d’une applet de commande PowerShell

Voici comment créer une ressource Application Insights dans le centre de données Azure USA Est à l’aide de l’applet de commande [New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) :

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Créer des ressources Application Insights à l’aide d’un modèle Resource Manager

Voici comment créer une ressource Application Insights à l’aide d’un modèle Resource Manager.

### <a name="create-the-azure-resource-manager-template"></a>Créer le modèle Azure Resource Manager

Créez un fichier .json appelé `template1.json` dans cet exemple. Copiez-y ce contenu :

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "retentionInDays": "[parameters('retentionInDays')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Utiliser le modèle Resource Manager pour créer une ressource Application Insights

1. Dans PowerShell, connectez-vous à Azure via `$Connect-AzAccount`
2. Affectez un abonnement à votre contexte avec `Set-AzContext "<subscription ID>"`
2. Exécutez un nouveau déploiement pour créer une ressource Application Insights :
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` est le groupe dans lequel vous souhaitez créer les nouvelles ressources.
   * `-TemplateFile` doit se produire avant les paramètres personnalisés.
   * `-appName` est le nom de la ressource à créer.

Vous pouvez ajouter d’autres paramètres. Vous trouverez leurs descriptions dans la section Paramètres du modèle.

## <a name="get-the-instrumentation-key"></a>Obtenir la clé d’instrumentation

Après avoir créé une ressource d’application, vous voulez obtenir la clé d’instrumentation : 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Pour voir la liste des nombreuses autres propriétés de votre ressource Application Insights, utilisez :

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Des propriétés supplémentaires sont disponibles via les applets de commande :
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Consultez la [documentation détaillée](https://docs.microsoft.com/powershell/module/az.applicationinsights) pour connaître les paramètres de ces applets de commande.  

## <a name="set-the-data-retention"></a>Définir la conservation des données 

Pour obtenir la conservation des données actuelle de votre ressource Application Insights, vous pouvez utiliser l’outil OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Découvrez plus en détail ARMClient dans les articles de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) et [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Voici un exemple d’utilisation de `ARMClient` pour obtenir la conservation actuelle :

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Pour définir la conservation, la commande est un PUT similaire :

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Pour définir la conservation des données sur 365 jours à l’aide du modèle ci-dessus, exécutez :

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

Le script suivant peut également être utilisé pour modifier la rétention. Copiez ce script pour l’enregistrer en tant que `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Ce script peut être alors utilisé comme suit  :

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Définir le plafond quotidien

Pour accéder aux propriétés du plafond quotidien, utilisez l’applet de commande [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) : 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Pour définir les propriétés du plafond quotidien, utilisez la même applet de commande. Par exemple, pour définir le plafond à 300 Go/jour, 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Définir le plan tarifaire 

Pour obtenir le plan tarifaire actuel, utilisez l’applet de commande [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) : 

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Pour définir le plan tarifaire, utilisez la même applet de commande avec le `-PricingPlan` spécifié :  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Vous pouvez également définir le plan tarifaire en fonction d’une ressource Application Insights existante à l’aide du modèle Resource Manager ci-dessus, en omettant la ressource « microsoft.insights/components » et le nœud `dependsOn` dans la ressource de facturation. Par exemple, pour définir le plan tarifaire en fonction du plan Par Go (anciennement appelé Plan de base), exécutez :

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

|priceCode|Plan|
|---|---|
|1|Par Go (anciennement appelé Plan de base)|
|2|Par nœud (anciennement appelé Plan Entreprise)|

## <a name="add-a-metric-alert"></a>Ajouter une alerte métrique

Pour configurer une alerte de métrique en même temps que votre ressource d’application, fusionnez le code comme suit dans le fichier de modèle :

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Lorsque vous appelez le modèle, vous pouvez éventuellement ajouter ce paramètre :

    `-responseTime 2`

Bien entendu, vous pouvez paramétrer d’autres champs. 

Pour rechercher les noms de type et les détails de configuration d’autres règles d’alerte, créez une règle manuellement et examinez-la dans [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Ajouter un test de disponibilité

Cet exemple concerne un test Ping (pour tester une seule page).  

**Il existe deux parties** dans un test de disponibilité : le test lui-même et l’alerte qui vous informe des échecs.

Fusionnez le code suivant dans le fichier de modèle qui crée l’application.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Pour découvrir les codes pour d’autres emplacements de test ou pour automatiser la création de tests web plus complexes, créez un exemple manuellement, puis paramétrez ensuite le code à partir [d’Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Ajouter des ressources

Pour automatiser la création de toute autre ressource de tout type, créez un exemple manuellement, puis copiez-le et paramétrez son code à partir [d’Azure Resource Manager](https://resources.azure.com/). 

1. Ouvrez [Azure Resource Manager](https://resources.azure.com/). Accédez à votre ressource d’application qui se trouve après `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`. 
   
    ![Navigation dans Azure Resource Explorer](./media/powershell/01.png)
   
    *composants* sont les ressources Application Insights de base pour l’affichage des applications. Il existe des ressources distinctes pour les règles d’alertes et les tests web de disponibilité associés.
2. Copiez le JSON du composant dans l’emplacement approprié dans `template1.json`.
3. Supprimez les propriétés suivantes :
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Ouvrez les sections `webtests` et `alertrules`, puis copiez le contenu JSON des éléments individuels dans votre modèle. (N’effectuez pas de copie à partir des nœuds `webtests` ou `alertrules`, accédez aux éléments situés juste en dessous.)
   
    Chaque test web étant associé à une règle d’alerte, vous devez copier les deux.
   
    Vous pouvez également inclure des alertes sur des métriques. [Noms de métriques](powershell-alerts.md#metric-names).
5. Insérez cette ligne dans chaque ressource :
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Définir les paramètres du modèle
Vous devez à présent remplacer les noms spécifiques par des paramètres. Pour [définir les paramètres d’un modèle](../../azure-resource-manager/resource-group-authoring-templates.md), écrivez des expressions utilisant un [ensemble de fonctions d’assistance](../../azure-resource-manager/resource-group-template-functions.md). 

Comme vous ne pouvez pas définir uniquement les paramètres d’une portion de chaîne, utilisez `concat()` pour générer les chaînes.

Voici des exemples de substitutions que vous allez effectuer. Il existe plusieurs occurrences de chaque substitution. Il vous en faudra peut-être d’autres dans votre modèle. Ces exemples utilisent les paramètres et les variables que nous avons définis en haut du modèle.

| find | replace with |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minuscules) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Définir les dépendances entre les ressources
Azure doit configurer les ressources dans un ordre strict. Pour vous assurer de l’achèvement d’une installation avant que la suivante ne commence, ajoutez les lignes de dépendance :

* Dans la ressource du test de disponibilité :
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Dans la ressource d’alerte pour un test de disponibilité :
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Étapes suivantes
Autres articles sur l’automation :

* [Création d'une ressource Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) - méthode rapide sans utiliser de modèle.
* [Configurer des alertes](powershell-alerts.md)
* [Créer des tests web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Envoyer des diagnostics Azure vers Application Insights.](powershell-azure-diagnostics.md)
* [Déployer sur Azure à partir de GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Créer des annotations de version](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
