---
title: Panneau Paramètres d’Azure Application Insights Profiler | Microsoft Docs
description: Consultez l’état du profileur et démarrez des sessions de profilage.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: b656fb521ad72256e91de63e96aa261f1e94bd13
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081921"
---
# <a name="configure-application-insights-profiler"></a>Configurer Application Insights Profiler

## <a name="profiler-settings-page"></a>Page Paramètres du profileur

Vous pouvez ouvrir la page des paramètres du profileur à partir de la page Performances d’Application Insights en appuyant sur le bouton **Profiler**.

![Entrée du volet Configurer Profiler][configure-profiler-entry]

La page Configurer Application Insights Profiler contient quatre fonctionnalités : 
1. **Profiler maintenant** : un clic sur ce bouton provoque le démarrage des sessions de profilage pour toutes les applications qui sont liées à cette instance d’Application Insights.
1. **Applications liées** : liste des applications qui envoient le profileur à cette ressource Application Insights.
1. **Sessions en cours** : quand vous appuyez sur **Profiler maintenant**, l’état de la session s’affiche ici.
1. **Sessions de profilage récentes** : affiche des informations sur les dernières sessions de profilage.

![Profiler à la demande][profiler-on-demand]

## <a name="app-service-environments-ase"></a>App Service Environments (ASE)
En fonction de la configuration de votre environnement ASE, l’appel visant à vérifier l’état de l’agent peut être bloqué. Cette page indique que l’agent n’est pas en cours d’exécution alors qu’il est. Vous pouvez vérifier la tâche web sur votre application pour en être sûr. Mais si tous les paramètres d’application sont définis correctement et que l’extension de site App Insights est installée sur votre application, le profileur sera en cours d’exécution et les sessions de profilage récentes seront visibles dans la liste si le trafic vers votre application est adéquat.

## <a id="profileondemand"></a> Déclencher manuellement Profiler

Vous pouvez déclencher Profiler manuellement en un clic de bouton. Supposons que vous exécutez un test de performances web. Vous avez besoin de traces pour vous aider à comprendre comment votre application web fonctionne avec une certaine charge. Pouvoir contrôler le moment où les traces sont capturées est essentiel, car si vous savez quand le test de charge s’exécute, l’intervalle d’échantillonnage peut néanmoins ne pas correspondre à ce moment.
Les étapes suivantes montrent comment ce scénario fonctionne :

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Facultatif) Étape 1 : Générer du trafic vers votre application web en démarrant un test de performances web

Si votre application web a déjà du trafic entrant ou si vous voulez simplement générer du trafic manuellement, ignorez cette section et continuez à l’étape 2.

Accédez au portail Application Insights, **Configurer > Test des performances**. Cliquez sur le bouton Nouveau pour démarrer un nouveau test de performances.

![Créer un test de performances][create-performance-test]

Dans le volet **Nouveau test de performance**, configurez l’URL cible du test. Acceptez tous les paramètres par défaut et démarrez l’exécution du test de charge.

![Configurer le test de charge][configure-performance-test]

Vous voyez que le nouveau test est d’abord placé en file d’attente, avec un état « En cours d’exécution ».

![Le test de charge est envoyé et placé en file d’attente][load-test-queued]

![Le test de charge est en cours d’exécution][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Étape 2 : Démarrer Profiler à la demande

Une fois que le test de charge est en cours d’exécution, nous pouvons démarrer Profiler pour capturer des traces sur l’application web pendant qu’elle reçoit la charge.
Accédez au volet Configurer Profiler :


### <a name="step-3-view-traces"></a>Étape 3 : Affichage des traces

Une fois que l’exécution de Profiler est terminée, suivez les instructions de la notification pour accéder à la page Performances et voir les traces.

## <a name="troubleshooting-on-demand-profiler"></a>Résolution des problèmes de Profiler à la demande

Vous voyez parfois un message d’erreur de délai d’expiration de Profiler après une session à la demande :

![Erreur de délai d’expiration de Profiler][profiler-timeout]

Vous voyez cette erreur pour deux raisons possibles :

1. La session Profiler à la demande a réussi, mais Application Insights a pris plus de temps pour traiter les données collectées. Si les données n’ont pas été entièrement traitées dans les 15 minutes, le portail affiche un message de dépassement du délai d’expiration. Après un certain temps, les traces de Profiler sont néanmoins affichées. Si cela se produit, ignorez simplement le message d’erreur pour l’instant. Nous travaillons activement sur un correctif.

1. Votre application web a une version plus ancienne de l’agent Profiler, qui n’a pas la fonctionnalité « à la demande ». Si vous avez déjà activé Application Insights Profiler, vous devrez peut-être mettre à jour votre agent Profiler pour commencer à utiliser la fonctionnalité « à la demande ».
  
Suivez ces étapes pour vérifier et installer la version la plus récente de Profiler :

1. Accédez aux paramètres d’application App Services et vérifiez si les paramètres suivants sont définis :
    * **APPINSIGHTS_INSTRUMENTATIONKEY** : remplacez ceci par la clé d’instrumentation correcte pour Application Insights.
    * **APPINSIGHTS_PORTALINFO** : ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION** : 1.0.0. Si ces paramètres ne sont pas définis, accédez au volet d’activation d’Application Insights pour installer la dernière extension de site.

1. Accédez au volet Application Insights dans le portail App Services.

    ![Activer Application Insights dans le portail App Services][enable-app-insights]

1. Si vous voyez un bouton « Mettre à jour » dans la page suivante, cliquez dessus pour mettre à jour l’extension de site Application Insights qui va installer le dernier agent Profiler.

    ![Mettre à jour l’extension de site][update-site-extension]

1. Cliquez ensuite sur **Modifier** pour activer Profiler et sélectionnez **OK** pour enregistrer les modifications.

    ![Modifier et enregistrer Application Insights][change-and-save-appinsights]

1. Revenez à l’onglet **Paramètres de l’application** pour App Service pour vérifier que les éléments des paramètres d’application suivants sont bien définis :
    * **APPINSIGHTS_INSTRUMENTATIONKEY** : remplacez ceci par la clé d’instrumentation correcte pour Application Insights.
    * **APPINSIGHTS_PORTALINFO** : ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION** : 1.0.0

    ![Paramètres d’application pour Profiler][app-settings-for-profiler]

1. Si vous le souhaitez, vérifiez la version de l’extension et qu’aucune mise à jour n’est disponible.

    ![Rechercher une mise à jour de l’extension][check-for-extension-update]

## <a name="next-steps"></a>Étapes suivantes
[Activer Profiler et afficher les traces](profiler-overview.md ?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png