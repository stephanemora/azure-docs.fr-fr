---
title: Analyser les performances d’Azure App Service Java | Microsoft Docs
description: Surveillance des performances des applications pour les services d’application Azure utilisant Java. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: d673524b30eae13e24758aff23a68bd2b38c2e3e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700461"
---
# <a name="application-monitoring-for-azure-app-service-and-java"></a>Surveillance des applications pour Azure App Service et Java

La surveillance de vos applications web basées sur Java qui s’exécutent sur [Azure App Service](../../app-service/index.yml) ne nécessite aucune modification du code. Cet article explique pas à pas comment activer la supervision Azure Monitor Application Insights et vous donne des conseils d’automatisation du processus pour les déploiements à grande échelle.

## <a name="enable-application-insights"></a>Activer Application Insights

Il est recommandé d’utiliser le portail Azure pour activer la surveillance des applications pour les applications Java s’exécutant sur Azure App Service. L’activation de la surveillance des applications dans le portail Azure instrumentent automatiquement votre application avec Application Insights.  

### <a name="auto-instrumentation-through-azure-portal"></a>Instrumentation automatique à travers le portail Azure

Cette méthode ne nécessite aucune modification de code ni configuration avancée, ce qui en fait le moyen le plus simple de prendre en main la surveillance d’Azure App Service. Vous pouvez appliquer des configurations supplémentaires, puis en fonction de votre scénario spécifique, vous pouvez évaluer si une surveillance plus avancée via l’[instrumentation manuelle](./java-2x-get-started.md?tabs=maven) est nécessaire.

### <a name="enable-backend-monitoring"></a>Activer la surveillance backend

Vous pouvez activer la surveillance de vos applications Java exécutées dans Azure App Service tout simplement en un seul clic, sans avoir à modifier le code. Application Insights pour les applications Java est intégré à App Service sur Linux (conteneurs basés sur du code et conteneurs personnalisés) et à App Service sur Windows (applications basées sur du code). Il est important de connaître la manière dont votre application sera surveillée. L’intégration ajoute [Application Insights Java 3.x](./java-in-process-agent.md), et vous obtenez la télémétrie collectée automatiquement.

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure de votre service d’application, puis sélectionnez **Activer**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Capture d’écran de l’onglet Application Insights avec l’option Activer sélectionnée."::: 

2. Choisissez de créer une nouvelle ressource, ou sélectionnez une ressource Application Insights existante pour cette application.

    > [!NOTE]
    > Quand vous sélectionnez **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 

     :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Capture d’écran de la liste déroulante Modifier votre ressource."::: 

3. Cette étape n'est pas requise. Après avoir spécifié la ressource à utiliser, vous pouvez configurer l’agent Java. Si vous ne configurez pas l’agent Java, les configurations par défaut s’appliquent. 

    L’[ensemble complet de configurations](./java-standalone-config.md) étant disponible, vous n’avez qu’à coller un [fichier json](./java-standalone-config.md#an-example) valide. **Excluez la chaîne de connexion et les configurations en préversion** : vous pourrez ajouter les éléments qui se trouvent actuellement en préversion à mesure qu’ils seront mis à la disposition générale.

    Une fois que vous avez modifié les configurations à travers le portail Azure, la variable d’environnement APPLICATIONINSIGHTS_CONFIGURATION_FILE est remplie automatiquement et s’affiche dans le panneau de paramètres d’App Service. Cette variable contient le contenu complet du fichier json que vous avez collé dans la zone de texte de configuration du portail Azure pour votre application Java. 

    :::image type="content"source="./media/azure-web-apps-java/create-app-service-ai.png" alt-text="Capture d’écran de l’instrumentation de votre application."::: 
    

## <a name="enable-client-side-monitoring"></a>Activer la supervision côté client

Pour activer la supervision côté client pour votre application Java, vous devez [ajouter manuellement le SDK JavaScript côté client à votre application](./javascript.md).

## <a name="automate-monitoring"></a>Automatiser la supervision

### <a name="application-settings"></a>Paramètres de l’application

Pour activer la collecte de données de télémétrie avec Application Insights, il vous suffit de définir les paramètres suivants de l’application :

|Nom du paramètre d’application |  Définition | Valeur |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Contrôle la surveillance du runtime | `~2` pour Windows ou `~3` pour Linux |
|XDT_MicrosoftApplicationInsights_Java |  Indicateur de contrôle de l’inclusion de l’agent Java | 0 ou 1 applicable uniquement sous Windows
|APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL | Utilisez-le uniquement si vous avez besoin de déboguer l’intégration d’Application Insights avec App Service | debug

:::image type="content"source="./media/azure-web-apps-java/application-settings-java.png" alt-text="Capture d’écran de Paramètres d’application App Service avec les paramètres Application Insights disponibles."::: 

> [!NOTE]
> Le profileur et le débogueur d’instantané ne sont pas disponibles pour les applications Java

[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]

## <a name="troubleshooting"></a>Résolution des problèmes

Vous trouverez ci-après les étapes à suivre pas à pas pour résoudre les problèmes rencontrés avec les applications Java exécutées sur Azure App Services.

1. Vérifiez que le paramètre d’application `ApplicationInsightsAgent_EXTENSION_VERSION` est défini sur une valeur de « ~2 » sous Windows, « ~3 » sous Linux
1. Examinez le fichier journal pour vérifier que l’agent a bien démarré : accédez à https://yoursitename.scm.azurewebsites.net/, modifiez SSH pour spécifier le répertoire racine, le fichier journal est situé sous LogFiles/ApplicationInsights. 
  
    :::image type="content"source="./media/azure-web-apps-java/app-insights-java-status.png" alt-text="Capture d’écran du lien au-dessus de la page de résultats."::: 

1. Après l’activation de la supervision des applications pour votre application Java, vous pouvez vérifier que l’agent fonctionne en regardant les métriques en direct. Avant même de déployer une application sur App Service, vous verrez des requêtes de l’environnement. N’oubliez pas que l’ensemble complet des données de télémétrie ne sera disponible qu’après avoir déployé et exécuté votre application. 
1. Définissez la variable d’environnement APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL sur « déboguer » si vous ne voyez pas d’erreurs et qu’il n’y a pas de données de télémétrie
1. Parfois, la version la plus récente de l’agent Java d’Application Insights n’est pas disponible dans App Service. Il faut quelques mois pour que les dernières versions soient déployées dans toutes les régions. Si vous avez besoin de la dernière version de l’agent Java pour surveiller votre application dans App Service, vous pouvez charger l’agent manuellement : 
    * Chargez le fichier jar de l’agent Java sur App Service
        * Obtenez la dernière version d’[Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli)
        * Obtenez la dernière version de l’[agent Java d’Application Insights](./java-in-process-agent.md)
        * Déployez l’agent Java sur App Service. Exemple de commande pour déployer le jar de l’agent Java : `az webapp deploy --src-path applicationinsights-agent-{VERSION_NUMBER}.jar --target-path java/applicationinsights-agent-{VERSION_NUMBER}.jar --type static --resource-group {YOUR_RESOURCE_GROUP} --name {YOUR_APP_SVC_NAME}` ou utilisez [ce guide](../../app-service/quickstart-java.md?tabs=javase&pivots=platform-linux#configure-the-maven-plugin) pour effectuer le déploiement à l’aide du plug-in Maven
    * Une fois le fichier jar de l’agent chargé, accédez aux configurations d’App Service et ajoutez une nouvelle variable d’environnement, JAVA_OPTS, et définissez-en la valeur sur `-javaagent:D:/home/{PATH_TO_THE_AGENT_JAR}/applicationinsights-agent-{VERSION_NUMBER}.jar`
    * Désactivez Application Insights via l’onglet Application Insights
    * Redémarrer l’application

    > [!NOTE]
    > Si vous avez défini la variable d’environnement JAVA_OPTS, vous devez désactiver Application Insights dans le portail. Si vous préférez activer Application Insights à partir du portail, vous pouvez également vérifier que vous n’avez pas défini la variable JAVA_OPTS dans les paramètres de configuration d’App Service. 


[!INCLUDE [azure-web-apps-troubleshoot](../../../includes/azure-monitor-app-insights-azure-web-apps-troubleshoot.md)]

## <a name="release-notes"></a>Notes de publication

Pour obtenir les mises à jour et correctifs de bogues les plus récents, [consultez les notes de publication](web-app-extension-release-notes.md).

## <a name="next-steps"></a>Étapes suivantes

* [Surveiller Azure Functions avec Application Insights](monitor-functions.md).
* [Autorisation de l’envoi de diagnostics Azure](../agents/diagnostics-extension-to-application-insights.md) vers Application Insights.
* [Analyse des mesures d’intégrité du service](../data-platform.md) pour vous assurer que votre service est disponible et réactif.
* [Réceptions de notifications d’alerte](../alerts/alerts-overview.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisation [d’Application Insights pour les pages Web et les applications JavaScript](javascript.md) pour obtenir les données de télémétrie du client à partir des navigateurs qui consultent une page web.
* [Configuration des tests de disponibilité web](monitor-web-app-availability.md) , pour recevoir des alertes en cas d’interruption de votre site.