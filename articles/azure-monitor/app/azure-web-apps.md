---
title: Analyser les performances d’Azure App Service | Microsoft Docs
description: Analyse des performances des applications pour les applications web Azure. Graphique de charge et de temps de réponse, informations de dépendance et alertes sur les performances.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: a5aea15c2563a81f4e915baa1b332beda64a1a70
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970965"
---
# <a name="monitor-azure-app-service-performance"></a>Analyser les performances d’Azure App Service
Dans le [portail Azure](https://portal.azure.com) vous pouvez configurer l’analyse des performances de vos applications web, serveurs principaux mobiles et applications API dans [Azure App Service](../../app-service/overview.md). [Azure Application Insights](../../application-insights/app-insights-overview.md) instrumente votre application afin qu’elle envoie des données de télémétrie concernant ses activités au service Application Insights, où elles sont stockées et analysées. Les graphiques de mesure et les outils de recherche peuvent alors être utilisés pour aider à diagnostiquer les problèmes, améliorer les performances et évaluer l’utilisation.

## <a name="run-time-or-build-time"></a>En cours d’exécution ou en cours de création
Vous pouvez configurer la surveillance par l’instrumentation de l’application de deux manières :

* **En cours d’exécution** : vous pouvez sélectionner une extension de surveillance des performances lorsque votre application est déjà active. Il n’est pas nécessaire de reconstruire ou de réinstaller votre application. Vous obtenez un ensemble standard de packages qui analyse les temps de réponse, les taux de réussite, les exceptions, les dépendances, etc. 
* **En cours de création** : vous pouvez installer un package dans votre application en cours de développement. Cette option est plus souple. Outre les mêmes packages standard, vous pouvez écrire du code pour personnaliser les données de télémétrie ou pour envoyer vos propres données de télémétrie. Vous pouvez consigner des activités spécifiques ou enregistrer les événements en fonction de la sémantique de votre domaine d’application. 

## <a name="run-time-instrumentation-with-application-insights"></a>Instrumentation d’exécution avec Application Insights
Si vous exécutez déjà une application web dans Azure, vous obtenez déjà certains éléments de surveillance, tels que le taux de demande et d’erreur. Ajoutez Application Insights pour bénéficier d’un plus grand nombre de fonctionnalités, telles que les temps de réponse, la surveillance des appels vers les dépendances, la détection intelligente et le puissant langage de requête Log Analytics. 

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure pour votre application web.

    ![Sous Paramètres, choisissez Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Choisissez de créer une nouvelle ressource, sauf si vous avez déjà configuré une ressource Application Insights pour cette application. 

    > [!NOTE]
    > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre application web et **déclenche un redémarrage de votre application web**. 

    ![Instrumenter votre application web](./media/azure-web-apps/create-resource.png)

2. Après avoir spécifié la ressource à utiliser, vous pouvez choisir la façon dont Application Insights doit collecter les données par plateforme pour votre application.

    ![Choisir les options par plateforme](./media/azure-web-apps/choose-options.png)

3. **Instrumentez votre application web** après l’installation d’Application Insights.

   **Activer l’analyse côté client** pour la consultation de page et la télémétrie de l’utilisateur.

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

* Dans Visual Studio, cliquez avec le bouton droit sur le projet, choisissez **Configurer Application Insights** et sélectionnez la ressource de votre choix. Vous avez la possibilité de créer une nouvelle ressource. Procédez maintenant à la régénération et au redéploiement.

## <a name="more-telemetry"></a>Données de télémétrie supplémentaires

* [Données de chargement de pages web](../../azure-monitor/app/javascript.md)
* [Télémétrie personnalisée](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Étapes suivantes
* [Exécuter le profileur sur une application dynamique](../../application-insights/app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - analyse les fonctions Azure avec Application Insights
* [Autorisation de l’envoi de diagnostics Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) vers Application Insights.
* [Analyse des mesures d’intégrité du service](../../azure-monitor/platform/data-collection.md) pour vous assurer que votre service est disponible et réactif.
* [Réceptions de notifications d’alerte](../../azure-monitor/platform/alerts-overview.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisation [d’Application Insights pour les pages Web et les applications JavaScript](../../azure-monitor/app/javascript.md) pour obtenir les données de télémétrie du client à partir des navigateurs qui consultent une page web.
* [Configuration des tests de disponibilité web](../../azure-monitor/app/monitor-web-app-availability.md) , pour recevoir des alertes en cas d’interruption de votre site.

