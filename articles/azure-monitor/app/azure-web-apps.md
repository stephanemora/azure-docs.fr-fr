---
title: Analyser les performances d’Azure App Service | Microsoft Docs
description: Analyse des performances des applications pour les services d’application Azure. Graphique de charge et de temps de réponse, informations de dépendance et alertes sur les performances.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122163"
---
# <a name="monitor-azure-app-service-performance"></a>Analyser les performances d’Azure App Service
Dans le [portail Azure](https://portal.azure.com), vous pouvez configurer la supervision des performances de vos applications web, des back-ends mobiles et des applications API dans [Azure App Service](../../app-service/overview.md). [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) instrumente votre application afin qu’elle envoie des données de télémétrie concernant ses activités au service Application Insights, où elles sont stockées et analysées. Les graphiques de mesure et les outils de recherche peuvent alors être utilisés pour aider à diagnostiquer les problèmes, améliorer les performances et évaluer l’utilisation.

## <a name="runtime-or-build-time"></a>Temps de runtime ou de la build
Vous pouvez configurer la surveillance par l’instrumentation de l’application de deux manières :

* **Runtime** -vous pouvez sélectionner une extension d’analyse lorsque votre service d’application est déjà en ligne des performances. Il n’est pas nécessaire de regénérer ou de réinstaller votre application. Vous obtenez un ensemble standard de packages qui analyse les temps de réponse, les taux de réussite, les exceptions, les dépendances, etc.

* **En cours de création** : vous pouvez installer un package dans votre application en cours de développement. Cette option est plus souple. Outre les mêmes packages standard, vous pouvez écrire du code pour personnaliser les données de télémétrie ou pour envoyer vos propres données de télémétrie. Vous pouvez consigner des activités spécifiques ou enregistrer les événements en fonction de la sémantique de votre domaine d’application. Cela vous donne également la possibilité de tester la dernière version du kit SDK Application Insights que vous pouvez choisir d’évaluer la version bêta de kits de développement logiciel tandis que le contrôle d’exécution est limité à la dernière version stable.

## <a name="runtime-instrumentation-with-application-insights"></a>Instrumentation du runtime avec Application Insights
Si vous utilisez actuellement un service d’application dans Azure, vous obtenez déjà certaines analyses : taux de demande et d’erreur par défaut. Ajouter Application Insights à tirer le meilleur, telles que les temps de réponse, surveillance des appels aux dépendances, détection intelligente et accéder au puissant langage de requête Kusto. 

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure pour votre service d’application.

    ![Sous Paramètres, choisissez Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Choisissez de créer une nouvelle ressource, sauf si vous avez déjà configuré une ressource Application Insights pour cette application. 

     > [!NOTE]
     > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

     ![Instrumenter votre application web](./media/azure-web-apps/create-resource.png)

2. Après avoir spécifié la ressource à utiliser, vous pouvez choisir la façon dont Application Insights doit collecter les données par plateforme pour votre application. Surveillance des applications ASP.NET est activé par défaut avec deux niveaux différents de la collection.

    ![Choisir les options par plateforme](./media/azure-web-apps/choose-options-new.png)

   * Le niveau de **collecte De base** .NET fournit les fonctionnalités APM principales pour une instance unique.
    
   * Le niveau de **collecte Recommandé** .NET :
       * Ajoute les tendances d’utilisation de l’UC, de la mémoire et des E/S.
       * Met en corrélation les microservices entre les limites de requête/dépendance.
       * Collecte les tendances d’utilisation et permet la mise en corrélation des résultats de la disponibilité avec les transactions.
       * Collecte les exceptions non gérées par le processus hôte.
       * Améliore la précision des métriques APM avec charge, quand l’échantillonnage est utilisé.
    
     .NET core offre **recommandé collection** ou **désactivé** pour .NET Core 2.0 et 2.1.

3. **Instrumentez votre service d’application** après l’installation d’Application Insights.

   **Activez la supervision côté client** pour la télémétrie des consultations de pages et des utilisateurs.

    (Cette option est activée par défaut pour les applications .NET Core configurées avec le niveau de **collecte Recommandé**, que le paramètre d’application « APPINSIGHTS_JAVASCRIPT_ENABLED » soit défini ou non. Une prise en charge précise via l’interface utilisateur permettant de désactiver la supervision côté client n’est pas disponible actuellement dans .NET Core.)
    
   * Cliquez sur Paramètres > Paramètres de l’application
   * Sous Paramètres de l’application, ajoutez une nouvelle paire clé/valeur :

     Clé :`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valeur: `true`
   * **Enregistrez** les paramètres et **Redémarrez** votre application.

4. Explorez les données de supervision de votre application en sélectionnant **Paramètres** > **Application Insights** > **Afficher plus dans Application Insights**.

Par la suite, vous pourrez, si vous le souhaitez, générer l’application avec Application Insights.

*Comment supprimer Application Insights, ou basculer vers l’envoi vers une autre ressource ?*

* Dans Azure, ouvrez le panneau de contrôle de l’application web et, sous Outils, ouvrez **Application Insights**. Vous pouvez désactiver Application Insights en cliquant sur **Désactiver** en haut, ou sélectionner une nouvelle ressource dans la section **Changer votre ressource**.

## <a name="build-the-app-with-application-insights"></a>Générez l’application avec Application Insights
Application Insights peut fournir des données de télémétrie détaillée par l’installation d’un SDK dans votre application. En particulier, vous pouvez collecter les journaux de suivi, [écrire une télémétrie personnalisée](../../azure-monitor/app/api-custom-events-metrics.md), et obtenir des rapports d’exception plus détaillées.

1. **Dans Visual Studio** (2013 mise à jour 2 ou ultérieure), configurez Application Insights pour votre projet.

    Cliquez avec le bouton droit sur le projet web et sélectionnez **Ajouter > Application Insights** ou **Projet** > **Application Insights** > **Configurer Application Insights**.

    ![Cliquez avec le bouton droit sur le projet web et sélectionnez Ajouter ou Configurer Application Insights.](./media/azure-web-apps/03-add.png)

    Si vous êtes invité à vous connecter, utilisez les informations d'identification de votre compte Azure.

    Cette opération a deux effets :

   1. Création d’une ressource Application Insights dans Azure, à l’endroit où vos données de télémétrie sont stockées, analysées et affichées.
   2. Ajout du package NuGet Application Insights à votre code (si ce n’est pas déjà fait), et configuration du package pour l’envoi de données de télémétrie à la ressource Azure.
2. **Testez la télémétrie** en exécutant l’application sur votre ordinateur de développement (F5).
3. **Publiez l’application** sur Azure comme d’habitude. 

*Comment modifier la configuration pour envoyer des données vers une autre ressource Application Insights ?*

* Dans Visual Studio, cliquez avec le bouton droit sur le projet, choisissez **Configurer Application Insights**, puis sélectionnez la ressource de votre choix. Vous avez la possibilité de créer une nouvelle ressource. Procédez maintenant à la régénération et au redéploiement.

## <a name="automate-monitoring"></a>Automatiser la surveillance

Pour activer la collecte des données de télémétrie avec Application Insights uniquement les paramètres d’Application doivent être définie :

   ![Paramètres d’Application app Service avec les paramètres d’Application Insights disponibles](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Définitions de paramètres d’application

|Nom du paramètre d’application |  Définition | Valeur |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extension principale, qui contrôle le contrôle d’exécution. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Par défaut, les fonctionnalités essentielles, uniquement en mode sont activées afin de s’assurer des performances optimales. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Contrôle si le moteur de réécriture binaire `InstrumentationEngine` est activé. Ce paramètre affecte les performances et a un impact sur les temps de démarrage/démarrage à froid. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Contrôle si le texte de table SQL et Azure est capturés, ainsi que les appels de dépendance. Avertissement de performance : cela nécessite la `InstrumentationEngine`. | `~1` |

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

Pour obtenir un exemple d’un gestionnaire de ressources Azure modèle comportant des paramètres de l’Application configurée pour Application Insights cela [modèle](https://github.com/Andrew-MSFT/BasicImageGallery) peut être utile, en particulier la section démarrage sur [ligne 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatiser la création d’une ressource Application Insights et un lien à votre application de Service nouvellement créé.

Pour créer un modèle Azure Resource Manager avec tous les paramètres d’Application Insights par défaut configurés, commencer le processus comme si vous souhaitez créer une application Web avec Application Insights est activé.

Sélectionnez **options d’automatisation**

   ![Menu de la création de App Service web app](./media/azure-web-apps/create-web-app.png)

Cela génère le dernier modèle Azure Resource Manager avec tous les paramètres requis configurés.

  ![Modèle d’application web App Service](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> Le modèle génère les paramètres de l’application en mode de « default ». Ce mode est à performances optimisées, bien que vous pouvez modifier le modèle pour activer quelle que soit la fonctionnalités que vous préférez.

## <a name="more-telemetry"></a>Données de télémétrie supplémentaires

* [Données de chargement de pages web](../../azure-monitor/app/javascript.md)
* [Télémétrie personnalisée](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>J’ai besoin accéder aux Extensions - ajouter - extension Application Insights pour les nouvelles applications App Service ?

Non, vous n’avez plus besoin d’ajouter l’extension manuellement. Activer Application Insights via le panneau Paramètres ajoutera tous les paramètres d’Application nécessaires pour activer la surveillance. Ceci est désormais possible, car les fichiers précédemment ajoutés par l’extension sont maintenant [préinstallé](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) en tant que partie de l’image d’App Service. Les fichiers sont situés dans `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>Si le runtime et la surveillance des temps de génération sont toutes deux activées je me retrouve avec des données en double ?

Non, par défaut si la surveillance des temps de génération est détecté par le biais de l’extension de contrôle d’exécution cesse d’envoyer des données et uniquement le temps de génération analyse de configuration sera honorée. La détermination de s’il faut désactiver l’analyse du runtime repose sur la détection d’une de ces trois fichiers :

* Microsoft.ApplicationInsights dll
* Dll de Microsoft.ASP.NET.TelemetryCorrelation
* System.Diagnostics.DiagnosticSource dll

Il est important de garder à l’esprit que de nombreuses versions de Visual Studio, tout ou partie de ces fichiers sont ajoutés par défaut pour les fichiers de modèle ASP.NET et ASP.NET Core Visual Studio. Si votre projet a été créé lorsqu’un des modèles, même si vous n’avez pas explicitement activé Application Insights, la présence de la dépendance de fichier empêcherait de contrôle d’exécution à partir de l’activation.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED entraîne une réponse HTML incomplète dans les applications web .NET CORE.

L’activation de Javascript via App Services peut provoquer des réponses HTML tronquées.

* Solution de contournement 1 : définissez le paramètre d’application APPINSIGHTS_JAVASCRIPT_ENABLED sur false, ou supprimez-le complètement et redémarrez
* Solution de contournement 2 : ajouter le Kit de développement logiciel par le biais de code et supprimer l’extension (débogueur de capture instantanée et de Profiler ne fonctionne pas avec cette configuration)

Pour suivre ce problème, accédez à [extension Azure à l’origine de la réponse HTML incomplète](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

.NET Core **ne prend pas en charge** :

* Le déploiement autonome
* Les applications ciblant .NET Framework
* Les applications .NET Core 2.2

> [!NOTE]
> .NET Core 2.0 et .NET Core 2.1 sont pris en charge. Cet article sera mis à jour après l’ajout de la prise en charge de .NET Core 2.2.

## <a name="next-steps"></a>Étapes suivantes
* [Exécuter le profileur sur une application dynamique](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - analyse les fonctions Azure avec Application Insights
* [Autorisation de l’envoi de diagnostics Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) vers Application Insights.
* [Analyse des mesures d’intégrité du service](../../azure-monitor/platform/data-collection.md) pour vous assurer que votre service est disponible et réactif.
* [Réceptions de notifications d’alerte](../../azure-monitor/platform/alerts-overview.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisation [d’Application Insights pour les pages Web et les applications JavaScript](../../azure-monitor/app/javascript.md) pour obtenir les données de télémétrie du client à partir des navigateurs qui consultent une page web.
* [Configuration des tests de disponibilité web](../../azure-monitor/app/monitor-web-app-availability.md) , pour recevoir des alertes en cas d’interruption de votre site.