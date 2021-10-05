---
title: Analyser les performances d’Azure App Service Node.js | Microsoft Docs
description: Surveillance des performances des applications pour les services d’application Azure utilisant Node.js. Analysez la charge, le temps de réponse et les dépendances dans des graphiques, et définissez des alertes sur les performances.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 5f80666d4a184da40979a38f6d7f17782291c9e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700455"
---
# <a name="application-monitoring-for-azure-app-service-and-nodejs"></a>Surveillance des applications pour Azure App Service et Node.js

Activer la supervision de vos applications web Node.js exécutées sur [Azure App Services](../../app-service/index.yml) n’a jamais été aussi facile. Alors qu’auparavant vous deviez instrumenter manuellement votre application, la dernière version de l’agent/extension est désormais intégrée à l’image App Service par défaut. Cet article explique pas à pas comment activer la supervision Azure Monitor Application Insights et vous donne des conseils d’automatisation du processus pour les déploiements à grande échelle.

> [!NOTE]
> Si les deux méthodes, la supervision basée sur un agent et l’instrumentation manuelle basée sur un Kit de développement logiciel (SDK), sont détectées, seuls les paramètres de l’instrumentation manuelle sont appliqués. Cela évite que des données en double soient envoyées. Pour en savoir plus, consultez la [section de résolution des problèmes](#troubleshooting).

## <a name="enable-agent-based-monitoring"></a>Activer la supervision basée sur un agent

Vous pouvez surveiller vos applications Node.js exécutées dans Azure App Service sans modifier le code, en quelques étapes simples. Application Insights pour les applications Node.js est intégré à App Service sur Linux (conteneurs basés sur du code et conteneurs personnalisés) et à App Service sur Windows (applications basées sur du code). L’intégration est en préversion publique. L’intégration ajoute le Kit de développement logiciel (SDK) Node.js, qui est en disponibilité générale. 

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure de votre service d’application, puis sélectionnez **Activer**.

    :::image type="content"source="./media/azure-web-apps/enable.png" alt-text="Capture d’écran de l’onglet Application Insights avec l’option Activer sélectionnée."::: 

2. Choisissez de créer une nouvelle ressource, ou sélectionnez une ressource Application Insights existante pour cette application.

     > [!NOTE]
     > Quand vous cliquez sur **OK** pour créer la ressource, vous êtes invité à **Appliquer les paramètres de supervision**. Si vous sélectionnez **Continuer**, votre nouvelle ressource Application Insights est liée à votre service d’application et **déclenche un redémarrage de votre service d’application**. 
    
    :::image type="content"source="./media/azure-web-apps/change-resource.png" alt-text="Capture d’écran de la liste déroulante Modifier votre ressource."::: 
    
3. Une fois que vous avez spécifié la ressource à utiliser, vous êtes prêt à vous lancer. 


    :::image type="content"source="./media/azure-web-apps-nodejs/app-service-node.png" alt-text="Capture d’écran de l’instrumentation de votre application."::: 


## <a name="enable-client-side-monitoring"></a>Activer la supervision côté client

Pour activer la supervision côté client pour votre application Node.js, vous devez [ajouter manuellement le SDK JavaScript côté client à votre application](./javascript.md).

## <a name="automate-monitoring"></a>Automatiser la supervision

Pour activer la collecte de données de télémétrie avec Application Insights, il vous suffit de définir les paramètres de l’application :

:::image type="content"source="./media/azure-web-apps-nodejs/application-settings-nodejs.png" alt-text="Capture d’écran de Paramètres d’application App Service avec les paramètres Application Insights disponibles."::: 


### <a name="application-settings-definitions"></a>Définitions des paramètres d’application

|Nom du paramètre d’application |  Définition | Valeur |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extension principale, qui contrôle la supervision runtime. | `~2` sous Windows ou `~3` sous Linux |
|XDT_MicrosoftApplicationInsights_NodeJS |  Indicateur pour contrôler si l’agent node.js est inclus. | 0 ou 1 applicable uniquement sous Windows. |


[!INCLUDE [azure-web-apps-arm-automation](../../../includes/azure-monitor-app-insights-azure-web-apps-arm-automation.md)]


## <a name="troubleshooting"></a>Résolution des problèmes

Vous trouverez ci-après les étapes à suivre pas à pas pour résoudre les problèmes rencontrés avec la supervision basée sur un agent/une extension pour les applications Node.js exécutées sur Azure App Services.

# <a name="windows"></a>[Windows](#tab/windows)

1. Vérifiez que le paramètre d’application `ApplicationInsightsAgent_EXTENSION_VERSION` est défini à une valeur « ~2 ».
2. Accédez à `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.  

    :::image type="content"source="./media/azure-web-apps/app-insights-sdk-status.png" alt-text="Capture d’écran du lien au-dessus de la page de résultats."border ="false"::: 

    - Vérifiez que l’extension est en cours d’exécution (`Application Insights Extension Status` affiche `Pre-Installed Site Extension, version 2.8.x.xxxx, is running.`) 

         Si elle ne l’est pas, suivez les instructions pour [Activer la surveillance d’Application Insights](#enable-agent-based-monitoring).

    - Accédez à *D:\local\Temp\status.json* et ouvrez *status.json*.

    Vérifiez que la valeur `SDKPresent` est définie sur false, la valeur `AgentInitializedSuccessfully` sur true et que la valeur `IKey` dispose d’une iKey valide.

    Voici un exemple du fichier JSON :

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    Si la valeur `SDKPresent` est true, cela indique que l’extension a détecté que certains éléments du SDK sont déjà présents dans l’application, et va s’interrompre.


# <a name="linux"></a>[Linux](#tab/linux)

1. Vérifiez que le paramètre d’application `ApplicationInsightsAgent_EXTENSION_VERSION` est défini sur une valeur « ~3 ».
2. Accédez à */var/log/applicationinsights/* et ouvrez *status.json*.

    Vérifiez que la valeur `SDKPresent` est définie sur false, la valeur `AgentInitializedSuccessfully` sur true et que la valeur `IKey` dispose d’une iKey valide.

    Voici un exemple du fichier JSON :

    ```json
        "AppType":"node.js",
                
        "MachineName":"c89d3a6d0357",
                
        "PID":"47",
                
        "AgentInitializedSuccessfully":true,
                
        "SDKPresent":false,
                
        "IKey":"00000000-0000-0000-0000-000000000000",
                
        "SdkVersion":"1.8.10"
    
    ```

    Si la valeur `SDKPresent` est true, cela indique que l’extension a détecté que certains éléments du SDK sont déjà présents dans l’application, et va s’interrompre.
---

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