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
ms.date: 01/29/2019
ms.author: mbullwin
ms.openlocfilehash: 4ec6e1288b34f04350a88697ca13ed044922ee50
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002238"
---
# <a name="monitor-azure-app-service-performance"></a>Analyser les performances d’Azure App Service
Dans le [portail Azure](https://portal.azure.com), vous pouvez configurer la supervision des performances de vos applications web, des back-ends mobiles et des applications API dans [Azure App Service](../../app-service/overview.md). [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) instrumente votre application afin qu’elle envoie des données de télémétrie concernant ses activités au service Application Insights, où elles sont stockées et analysées. Les graphiques de mesure et les outils de recherche peuvent alors être utilisés pour aider à diagnostiquer les problèmes, améliorer les performances et évaluer l’utilisation.

## <a name="run-time-or-build-time"></a>En cours d’exécution ou en cours de création
Vous pouvez configurer la surveillance par l’instrumentation de l’application de deux manières :

* **Runtime** : vous pouvez sélectionner une extension d’analyse des performances lorsque votre service d’application est déjà actif. Il n’est pas nécessaire de regénérer ou de réinstaller votre application. Vous obtenez un ensemble standard de packages qui analyse les temps de réponse, les taux de réussite, les exceptions, les dépendances, etc. 
* **En cours de création** : vous pouvez installer un package dans votre application en cours de développement. Cette option est plus souple. Outre les mêmes packages standard, vous pouvez écrire du code pour personnaliser les données de télémétrie ou pour envoyer vos propres données de télémétrie. Vous pouvez consigner des activités spécifiques ou enregistrer les événements en fonction de la sémantique de votre domaine d’application. 

## <a name="run-time-instrumentation-with-application-insights"></a>Instrumentation d’exécution avec Application Insights
Si vous exécutez déjà un service d’application dans Azure, vous obtenez déjà certains éléments de surveillance, tels que le taux de demande et d’erreur. Ajoutez Application Insights pour bénéficier d’un plus grand nombre de fonctionnalités, telles que les temps de réponse, la surveillance des appels vers les dépendances, la détection intelligente et le puissant langage de requête Data Explorer. 

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure pour votre service d’application.

    ![Sous Paramètres, choisissez Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Choisissez de créer une nouvelle ressource, sauf si vous avez déjà configuré une ressource Application Insights pour cette application. 

    > [!NOTE]
    > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

    ![Instrumenter votre application web](./media/azure-web-apps/create-resource.png)

2. Après avoir spécifié la ressource à utiliser, vous pouvez choisir la façon dont Application Insights doit collecter les données par plateforme pour votre application. (La supervision d’applications ASP.NET est activée par défaut avec deux niveaux de collecte différents.)

    ![Choisir les options par plateforme](./media/azure-web-apps/choose-options-new.png)

    * Le niveau de **collecte De base** .NET fournit les fonctionnalités APM principales pour une instance unique.
    
    * Le niveau de **collecte Recommandé** .NET :
        * Ajoute les tendances d’utilisation de l’UC, de la mémoire et des E/S.
        * Met en corrélation les microservices entre les limites de requête/dépendance.
        * Collecte les tendances d’utilisation et permet la mise en corrélation des résultats de la disponibilité avec les transactions.
        * Collecte les exceptions non gérées par le processus hôte.
        * Améliore la précision des métriques APM avec charge, quand l’échantillonnage est utilisé.
    
    .NET Core prend en charge le niveau de **collecte Recommandé** ou Désactivé pour .NET Core 2.0 et 2.1.

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

## <a name="more-telemetry"></a>Données de télémétrie supplémentaires

* [Données de chargement de pages web](../../azure-monitor/app/javascript.md)
* [Télémétrie personnalisée](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>Résolution de problèmes

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED entraîne une réponse HTML incomplète dans les applications web .NET CORE.

L’activation de Javascript via App Services peut provoquer des réponses HTML tronquées.

* Solution de contournement 1 : définissez le paramètre d’application APPINSIGHTS_JAVASCRIPT_ENABLED sur false, ou supprimez-le complètement et redémarrez
* Solution de contournement 2 : ajoutez le SDK via du code et supprimez l’extension (les fonctionnalités de débogueur de capture instantanée et de profileur ne fonctionne pas avec cette configuration)

Ce problème est suivi [ici](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

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

