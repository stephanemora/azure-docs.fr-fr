---
title: Analyser les performances d’Azure App Service | Microsoft Docs
description: Analyse des performances des applications pour les services d’application Azure. Graphique de charge et les temps de réponse, les informations de dépendance et définir des alertes sur les performances.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: c616b2578f7606ce7df19fdbef16bec8a24428d3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262497"
---
# <a name="monitor-azure-app-service-performance"></a>Analyser les performances d’Azure App Service

Activer l’analyse des applications web en s’exécutant sur Azure App Services sur votre .NET et le .NET Core est désormais plus facile que jamais. Alors que précédemment, vous deviez installer manuellement une extension de site, l’agent d’extension/dernière est désormais intégré dans l’image de service d’application par défaut. Cet article se vous guident dans l’activation de la surveillance Application Insights ainsi que fournir des conseils préliminaires pour automatiser le processus pour les déploiements à grande échelle.

> [!NOTE]
> Ajout manuel d’une extension de site Application Insights via **outils de développement** > **Extensions** est déconseillée. Cette méthode d’installation de l’extension a été dépend des mises à jour manuelles pour chaque nouvelle version. La dernière version stable de l’extension est désormais [préinstallé](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) en tant que partie de l’image d’App Service. Les fichiers sont situés dans `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` et sont automatiquement mis à jour avec chaque version stable. Si vous suivez les instructions en fonction de l’agent pour activer l’analyse ci-dessous, il supprimera automatiquement l’extension déconseillée pour vous.

## <a name="enable-application-insights"></a>Activer Application Insights

Il existe deux façons d’activer l’analyse des applications pour les applications Azure App Services hébergés :

* **Application basée sur un agent de surveillance** (ApplicationInsightsAgent).  
    * Cette méthode est la plus facile à activer, et aucune configuration avancée est requise. Il est souvent appelé « runtime » de surveillance. Pour Azure App Services nous recommandons au minimum l’activation de ce niveau de surveillance et ensuite selon votre scénario spécifique, que vous pouvez évaluer si une surveillance plus avancée via une instrumentation manuelle est nécessaire.

* **Manuellement l’instrumentation de l’application via le code** en installant le kit SDK Application Insights.

    * Cette approche est beaucoup plus personnalisable, mais elle nécessite [Ajout d’une dépendance sur les packages NuGet du Kit de développement logiciel Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Cette méthode, signifie également que vous avez à gérer les mises à jour vers la dernière version des packages.

    * Si vous avez besoin effectuer des appels d’API personnalisés pour suivre les événements/des dépendances ne pas capturées par défaut avec la surveillance basée sur un agent, vous devez utiliser cette méthode. Découvrez le [API pour les événements et les mesures d’article personnalisé](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) pour en savoir plus.

> [!NOTE]
> Si l’agent en fonction de surveillance et le Kit de développement logiciel manuels en fonction d’instrumentation est détectée qu'uniquement les paramètres d’une instrumentation manuelle seront honorées. Cela vise à éviter les données en double à partir d’envoyés. Pour en savoir plus sur cette extraction le [section Dépannage](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) ci-dessous.

## <a name="enable-agent-based-monitoring-net"></a>Activer en fonction de l’agent de surveillance .NET

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure pour votre service d’application.

    ![Sous Paramètres, choisissez Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Choisissez de créer une nouvelle ressource, sauf si vous avez déjà configuré une ressource Application Insights pour cette application. 

     > [!NOTE]
     > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

     ![Instrumenter votre application web](./media/azure-web-apps/create-resource-01.png)

2. Après avoir spécifié la ressource à utiliser, vous pouvez choisir la façon dont Application Insights doit collecter les données par plateforme pour votre application. Surveillance des applications ASP.NET est activé par défaut avec deux niveaux différents de la collection.

    ![Choisir les options par plateforme](./media/azure-web-apps/choose-options-new.png)

   * Le niveau de **collecte De base** .NET fournit les fonctionnalités APM principales pour une instance unique.

   * Le niveau de **collecte Recommandé** .NET :
       * Ajoute les tendances d’utilisation de l’UC, de la mémoire et des E/S.
       * Met en corrélation les microservices entre les limites de requête/dépendance.
       * Collecte les tendances d’utilisation et permet la mise en corrélation des résultats de la disponibilité avec les transactions.
       * Collecte les exceptions non gérées par le processus hôte.
       * Améliore la précision des métriques APM avec charge, quand l’échantillonnage est utilisé.

3. Pour configurer des paramètres tels que l’échantillonnage, que vous pouvez contrôler précédemment via le fichier applicationinsights.config, vous pouvez maintenant interagir avec ces mêmes paramètres par le biais de paramètres d’Application avec un préfixe correspondant. 

    * Par exemple, pour modifier le pourcentage d’échantillonnage initiale, vous pouvez créer un paramètre d’Application : `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` et la valeur `100`.

    * Pour la liste des paramètres de processeur de télémétrie d’échantillonnage ADAPTATIF pris en charge, vous pouvez consulter le [code](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) et [documentation associée](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Activer en fonction de l’agent de surveillance .NET Core

Les versions suivantes de .NET Core sont pris en charge : ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Ciblant le framework complet à partir de .NET Core, déploiement autonome et ASP.NET Core 3.0 sont actuellement **ne pas pris en charge** avec/extension de l’agent en fonction de surveillance. ([Une instrumentation manuelle](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) via le code fonctionne dans tous les scénarios précédents.)

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure pour votre service d’application.

    ![Sous Paramètres, choisissez Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Choisissez de créer une nouvelle ressource, sauf si vous avez déjà configuré une ressource Application Insights pour cette application. 

     > [!NOTE]
     > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

     ![Instrumenter votre application web](./media/azure-web-apps/create-resource-01.png)

2. Après avoir spécifié quelle ressource utiliser, vous pouvez choisir comment vous souhaitez qu’Application Insights pour collecter des données par la plateforme de votre application. .NET core offre **recommandé collection** ou **désactivé** pour .NET Core 2.0, 2.1 et 2.2.

    ![Choisir les options par plateforme](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Activez .NET de surveillance côté client

Analyse côté client est opt-in pour ASP.NET. Pour activer l’analyse côté client :

* Sélectionnez **paramètres** > ** ** Paramètres Application ***
   * Sous paramètres de l’Application, ajoutez un nouveau **nom de paramètre d’application** et **valeur**:

     Nom : `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valeur : `true`

   * **Enregistrez** les paramètres et **Redémarrez** votre application.

![Capture d’écran de l’application paramètres de l’interface utilisateur](./media/azure-web-apps/appinsights-javascript-enabled.png)

Pour désactiver l’analyse soit supprimer la paire de valeur de clé associée à partir des paramètres d’Application côté client, ou définir la valeur False.

## <a name="enable-client-side-monitoring-net-core"></a>Activer côté client analyse .NET Core

Analyse côté client est **activé par défaut** pour les applications .NET Core avec **recommandé collection**, indépendamment de si le paramètre « APPINSIGHTS_JAVASCRIPT_ENABLED » d’application est présente.

Si pour une raison quelconque, vous souhaitez désactiver l’analyse côté client :

* Sélectionnez **paramètres** > **paramètres d’Application**
   * Sous paramètres de l’Application, ajoutez un nouveau **nom de paramètre d’application** et **valeur**:

     name : `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valeur : `false`

   * **Enregistrez** les paramètres et **Redémarrez** votre application.

![Capture d’écran de l’application paramètres de l’interface utilisateur](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatiser la surveillance

Pour activer la collecte de données de télémétrie avec Application Insights, uniquement les paramètres d’Application doivent être définie :

   ![Paramètres d’Application app Service avec les paramètres d’Application Insights disponibles](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Définitions de paramètres d’application

|Nom du paramètre d’application |  Définition | Valeur |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extension principale, qui contrôle le contrôle d’exécution. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Par défaut, les fonctionnalités essentielles, uniquement en mode sont activées afin de s’assurer des performances optimales. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Contrôle si le moteur de réécriture binaire `InstrumentationEngine` est activé. Ce paramètre affecte les performances et a un impact sur les temps de démarrage/démarrage à froid. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Contrôle si le texte de table SQL et Azure est capturés, ainsi que les appels de dépendance. Avertissement de performance : ce paramètre requiert le `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Paramètres d’Application de Service application avec Azure Resource Manager

Paramètres d’application pour les Services d’application peuvent être gérés et configurés avec [modèles Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Cette méthode peut être utilisée lors du déploiement de nouvelles ressources App Service avec l’automatisation d’Azure Resource Manager, ou pour modifier les paramètres des ressources existantes.

La structure de base des paramètres d’application JSON pour un service d’application est ci-dessous :

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

Pour obtenir un exemple d’un modèle Azure Resource Manager avec les paramètres de l’Application configurée pour Application Insights, cela [modèle](https://github.com/Andrew-MSFT/BasicImageGallery) peut être utile, en particulier la section démarrage sur [ligne 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatiser la création d’une ressource Application Insights et un lien à votre application de Service nouvellement créé.

Pour créer un modèle Azure Resource Manager avec tous les paramètres d’Application Insights par défaut configurés, commencer le processus comme si vous souhaitez créer une application Web avec Application Insights est activé.

Sélectionnez **options d’automatisation**

   ![Menu de la création de App Service web app](./media/azure-web-apps/create-web-app.png)

Cette option génère le dernier modèle Azure Resource Manager avec tous les paramètres requis configurés.

  ![Modèle d’application web App Service](./media/azure-web-apps/arm-template.png)

Voici un exemple, remplacez toutes les instances de `AppMonitoredSite` avec le nom de votre site :

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
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Le modèle génère les paramètres de l’application en mode de « default ». Ce mode est à performances optimisées, bien que vous pouvez modifier le modèle pour activer quelle que soit la fonctionnalités que vous préférez.

### <a name="enabling-through-powershell"></a>L’activation via PowerShell

Pour permettre l’application de surveillance via PowerShell, uniquement les paramètres d’application sous-jacent doivent être modifiées. Voici un exemple, ce qui permet l’analyse des applications pour un site Web appelé « AppMonitoredSite » dans le groupe de ressources « AppMonitoredRG », et configure les données à envoyer à la clé d’instrumentation de « 012345678-abcd-ef01-2345-6789abcd ».

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Mise à niveau de l’agent d’extension de surveillance

### <a name="upgrading-from-versions-289-and-up"></a>La mise à niveau à partir de versions 2.8.9 et ultérieures

La mise à niveau à partir de la version 2.8.9 se produit automatiquement, sans aucune action supplémentaire. Les nouveaux bits de surveillance sont fournies en arrière-plan pour le service d’application cible, et lors du redémarrage de l’application qu’ils ne seront récupérés.

Pour vérifier quelle version de l’extension vous sont en cours d’exécution visite `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Capture d’écran de chemin d’url http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Mettre à niveau à partir de versions 1.0.0 - 2.6.5

L’extension de site préinstallé depuis la version 2.8.9 est utilisée. Si vous êtes une version antérieure, vous pouvez mettre à jour par le biais de deux manières :

* [Mise à niveau en activant via le portail](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Même si vous avez l’extension Application Insights pour Azure App Service est installé, l’interface utilisateur affiche uniquement **activer** bouton. Dans les coulisses, l’ancienne extension de site privé sera supprimée.)

* [Mise à niveau via PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Définir les paramètres d’application pour activer l’extension de site préinstallé ApplicationInsightsAgent. Consultez [l’activation via powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Supprimez manuellement l’extension de site privé nommée extension Application Insights pour Azure App Service.

Si la mise à niveau est effectuée à partir d’une version antérieure 2.5.1, vérifiez que les DLL ApplicationInsigths sont supprimés à partir du dossier bin d’application [consultez les étapes de dépannage](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Résolution de problèmes

Voici notre guide de dépannage pas à pas pour l’extension/agent en fonction de surveillance pour .NET et .NET Core en fonction des applications s’exécutant sur Azure App Services.

> [!NOTE]
> Les applications Java et Node.js sont uniquement pris en charge sur Azure App Services via une instrumentation manuelle SDK basé et par conséquent, les étapes ci-dessous ne s’appliquent pas à ces scénarios.

1. Vérifiez que l’application est surveillée par le biais de `ApplicationInsightsAgent`.
    * Vérifiez que `ApplicationInsightsAgent_EXTENSION_VERSION` paramètre d’application est défini sur une valeur de « ~ 2 ».
2. Assurez-vous que l’application remplit les conditions à surveiller.
    * Accédez à `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Capture d’écran de https://yoursitename.scm.azurewebsites/applicationinsights page de résultats](./media/azure-web-apps/app-insights-sdk-status.png)

    * Vérifiez que le `Application Insights Extension Status` est `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Si elle n’est pas en cours d’exécution, suivez les [activer Application Insights analyse les instructions](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Vérifiez que la source de l’état existe et qu’il ressemble à : `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Si une valeur similaire n’est pas présente, cela signifie que l’application n’est pas en cours d’exécution ou n’est pas pris en charge. Pour vous assurer que l’application est en cours d’exécution, essayez d’accéder manuellement à application/application url points de terminaison, ce qui permettront à la disposition, les informations d’exécution.

    * Vérifiez que `IKeyExists` est `true`
        * Si elle est false, ajoutez ' APPINSIGHTS_INSTRUMENTATIONKEY avec votre guid ikey à vos paramètres d’application.

    * Confirmer qu’il n’y a aucune entrée pour `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, et `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Si un de ces entrées existe, supprimez les packages suivants à partir de votre application : `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, et `Microsoft.AspNet.TelemetryCorrelation`.

Le tableau ci-dessous fournit une explication plus détaillée de la signifient de ces valeurs, leur sous-jacent entraîne et corrections recommandées :

|Valeur de problème|Explication|Correctif
|---- |----|---|
| `AppAlreadyInstrumented:true` | Cette valeur indique que l’extension a détecté que certains aspects du Kit de développement est déjà présent dans l’Application et sera temporisation. Il peut être dû à une référence à `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, ou `Microsoft.ApplicationInsights`  | Supprimez les références. Certaines de ces références sont ajoutées par défaut à partir de certains modèles Visual Studio et les versions antérieures de Visual Studio peuvent ajouter des références à `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Si l’application cible .NET Core 2.1 ou 2.2 et fait référence à [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) méta-package, puis il permet d’utiliser Application Insights, et extension sera temporisation. | Les clients sur .NET Core 2.1,2.2 [recommandé](https://github.com/aspnet/Announcements/issues/287) utiliser Microsoft.AspNetCore.App méta-package à la place.|
|`AppAlreadyInstrumented:true` | Cette valeur peut également être provoquée par la présence des DLL ci-dessus dans le dossier d’application à partir d’un déploiement précédent. | Nettoyer le dossier d’application pour vous assurer que ces DLL sont supprimées.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Cette valeur indique que l’extension détecté références à `Microsoft.AspNet.TelemetryCorrelation` dans l’application et sera temporisation. | Supprimez la référence.
|`AppContainsDiagnosticSourceAssembly**:true`|Cette valeur indique que l’extension détecté références à `System.Diagnostics.DiagnosticSource` dans l’application et sera temporisation.| Supprimez la référence.
|`IKeyExists:false`|Cette valeur indique que la clé d’instrumentation n’est pas présente dans l’élément AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Causes possibles : Les valeurs a peut-être été accidentellement supprimé, vous avez oublié de définir les valeurs de script d’automatisation, etc. | Assurez-vous que le paramètre est présent dans les paramètres d’application App Service.

Pour plus d’informations sur l’extension Application Insights agent, consultez le [notes de version](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Étapes suivantes
* [Exécuter le profileur sur une application dynamique](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - analyse les fonctions Azure avec Application Insights
* [Autorisation de l’envoi de diagnostics Azure](../platform/diagnostics-extension-to-application-insights.md) vers Application Insights.
* [Analyse des mesures d’intégrité du service](../platform/data-platform.md) pour vous assurer que votre service est disponible et réactif.
* [Réceptions de notifications d’alerte](../platform/alerts-overview.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisation [d’Application Insights pour les pages Web et les applications JavaScript](javascript.md) pour obtenir les données de télémétrie du client à partir des navigateurs qui consultent une page web.
* [Configuration des tests de disponibilité web](monitor-web-app-availability.md) , pour recevoir des alertes en cas d’interruption de votre site.
