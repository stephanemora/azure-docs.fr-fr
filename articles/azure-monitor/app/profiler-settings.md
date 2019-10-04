---
title: Utiliser le volet de paramètres d’Azure Application Insights Profiler | Microsoft Docs
description: Voir l’état de Profiler et démarrer des sessions de profilage
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 2bc32e28ffd6dd9dab2da61078684791a04709cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922987"
---
# <a name="configure-application-insights-profiler"></a>Configurer Application Insights Profiler

## <a name="profiler-settings-pane"></a>Volet des paramètres de Profiler

Pour ouvrir le volet des paramètres d’Azure Application Insights Profiler, accédez au volet Performances Application Insights, puis sélectionnez le bouton **Profiler**.

![Configurer le volet Profiler][configure-profiler-entry]

Le volet **Configurer Application Insights Profiler** contient quatre fonctionnalités : 
* **Profiler maintenant** : Démarre des sessions de profilage pour toutes les applications qui sont liées à cette instance d’Application Insights.
* **Applications liées** : Liste les applications qui envoient des données de profilage à cette ressource Application Insights.
* **Sessions en cours** : Affiche l’état de la session quand vous sélectionnez **Profiler maintenant**. 
* **Sessions de profilage récentes** : Affiche des informations sur les sessions de profilage effectuées.

![Profiler à la demande][profiler-on-demand]

## <a name="app-service-environment"></a>Environnement App Service
En fonction de la configuration de votre environnement Azure App Service, l’appel permettant de vérifier l’état de l’agent peut être bloqué. Le volet peut afficher un message indiquant que l’agent n’est pas exécuté même s’il est en cours d’exécution. Pour vérifier qu’il est exécuté, consultez la tâche web sur votre application. Si toutes les valeurs de paramètres d’application sont correctes et que l’extension de site Application Insights est installée sur votre application, Profiler est en cours d’exécution. Si votre application ne reçoit pas suffisamment de trafic, les sessions de profilage récentes doivent s’afficher dans une liste.

## <a id="profileondemand"></a> Déclencher manuellement Profiler

### <a name="minimum-requirements"></a>Configuration minimale requise 
Pour déclencher manuellement une session du profileur, l'utilisateur doit au moins disposer d'un accès en « écriture » au composant Application Insights. Dans la plupart des cas, vous bénéficiez automatiquement de cet accès et vous n'avez rien d'autre à faire. En cas de problèmes, le rôle d'étendue d'abonnement à ajouter est « Contributeur de composant Application Insights ». [En savoir plus sur le contrôle d'accès par rôle avec Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

Vous pouvez déclencher manuellement Profiler en un clic. Supposons que vous exécutez un test de performances web. Vous avez besoin des traces pour comprendre comment votre application web fonctionne avec une certaine charge. Pour savoir quand le test de charge est exécuté, vous avez besoin de contrôler le moment de capture des traces. Toutefois, l’intervalle d’échantillonnage aléatoire ne permet pas de le faire.

Les sections suivantes montrent comment ce scénario fonctionne :

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Étape 1 : (Facultatif) Générer du trafic vers votre application web en démarrant un test de performances web

Si votre application web a déjà du trafic entrant ou si vous voulez simplement générer du trafic manuellement, ignorez cette section et continuez à l’étape 2.

1. Dans le portail Application Insights, sélectionnez **Configurer** > **Test de performances**. 

1. Pour démarrer un nouveau test de performances, sélectionnez le bouton **Nouveau**.

   ![Créer un test de performances][create-performance-test]

1. Dans le volet **Nouveau test de performance**, configurez l’URL cible du test. Acceptez tous les paramètres par défaut, puis sélectionnez **Exécuter le test** pour lancer l’exécution du test de charge.

    ![Configurer le test de charge][configure-performance-test]

    Le nouveau test est mis le premier en file d’attente, suivi de l’état *En cours*.

    ![Le test de charge est envoyé et placé en file d’attente][load-test-queued]

    ![Le test de charge est en cours d’exécution][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Étape 2 : Démarrer une session de Profiler à la demande

1. Quand le test de charge est en cours d’exécution, démarrez Profiler pour capturer les traces de l’application web pendant qu’elle reçoit la charge.

1. Accédez au volet **Configurer Profiler**.


### <a name="step-3-view-traces"></a>Étape 3 : Affichage des traces

Une fois que l’exécution de Profiler est terminée, suivez les instructions de la notification pour accéder au volet Performances et voir les traces.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Résoudre les problèmes de la session de Profiler à la demande

Après une session à la demande, vous pouvez recevoir un message d’erreur d’expiration du délai d’attente de Profiler :

![Erreur de délai d’expiration de Profiler][profiler-timeout]

Vous pouvez recevoir cette erreur pour une des raisons suivantes :

* La session de Profiler à la demande a réussi, mais Application Insights a pris plus de temps que prévu pour traiter les données collectées.  

  Si les données ne sont pas traitées dans les 15 minutes, le portail affiche un message d’expiration du délai d’attente. Après un certain temps, les traces de Profiler sont affichées. Si vous recevez un message d’erreur, ignorez-le pour le moment. Nous travaillons activement sur un correctif.

* Votre application web a une version plus ancienne de l’agent Profiler, qui n’a pas la fonctionnalité « à la demande ».  

  Si vous aviez déjà activé Application Insights Profiler, vous devez peut-être mettre à jour votre agent Profiler pour commencer à utiliser la fonctionnalité « à la demande ».
  
Accédez au volet **Paramètres d’application** d’App Services et vérifiez les paramètres suivants :
* **APPINSIGHTS_INSTRUMENTATIONKEY** : remplacez ceci par la clé d’instrumentation correcte pour Application Insights.
* **APPINSIGHTS_PORTALINFO** : ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION** : 1.0.0

Si une des valeurs précédentes n’est pas définie, installez la dernière extension de site en effectuant les étapes suivantes :

1. Accédez au volet **Application Insights** dans le portail App Services.

    ![Activer Application Insights dans le portail App Services][enable-app-insights]

1. Si le volet **Application Insights** affiche un bouton **Mettre à jour**, sélectionnez-le pour mettre à jour l’extension de site Application Insights qui va installer le dernier agent Profiler.

    ![Mettre à jour l’extension de site][update-site-extension]

1. Pour vérifier que Profiler est activé, sélectionnez **Changer**, puis **OK** pour enregistrer les changements.

    ![Modifier et enregistrer Application Insights][change-and-save-appinsights]

1. Revenez au volet **Paramètres d’application** du service d’application pour vérifier que les valeurs suivantes sont définies :
   * **APPINSIGHTS_INSTRUMENTATIONKEY** : remplacez ceci par la clé d’instrumentation correcte pour Application Insights.
   * **APPINSIGHTS_PORTALINFO** : ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION** : 1.0.0

     ![Paramètres d’application pour Profiler][app-settings-for-profiler]

1. Vous pouvez aussi sélectionner **Extensions**, puis vérifier la version de l’extension et déterminer si une mise à jour est disponible.

    ![Rechercher une mise à jour de l’extension][check-for-extension-update]

## <a name="next-steps"></a>Étapes suivantes
[Activer Profiler et voir les traces](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

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
