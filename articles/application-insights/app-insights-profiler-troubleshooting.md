---
title: Résoudre les problèmes liés à Azure Application Insights Profiler | Microsoft Docs
description: Cette page contient des étapes de dépannage et des informations pour aider les développeurs qui rencontrent des difficultés lors de l’activation ou de l’utilisation d’Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 28de0f8bdcaa730c5beea0c630d4e86e15642809
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142854"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Résoudre les problèmes d’activation ou d’affichage d’Application Insights Profiler

## <a id="troubleshooting"></a>Résolution générale des problèmes

### <a name="profiles-are-only-uploaded-if-there-are-requests-to-your-application-while-the-profiler-is-running"></a>Les profils sont chargés uniquement si des requêtes sont envoyées à votre application pendant que le profileur est en cours d’exécution
Application Insights Profiler collecte des données de profileur pendant deux minutes chaque heure, ou quand vous appuyez sur le bouton [**Profiler maintenant**](app-insights-profiler-settings.md?toc=/azure/azure-monitor/toc.json) dans la page **Configurer Application Insights Profiler**. Mais les données de profilage sont chargées uniquement quand elles peuvent être jointes à une requête qui a eu lieu pendant l’exécution du profileur. 

Le profileur écrit les messages de trace et les événements personnalisés dans votre ressource Application Insights. Vous pouvez utiliser ces événements pour voir comment le profileur s’exécute. Si vous pensez que le profileur devrait être en cours d’exécution et capturer des traces, mais que vous ne les voyez pas dans la page Performance, vous pouvez vérifier comment le profileur s’exécute :

1. Recherchez des messages de trace et des événements personnalisés envoyés par le profileur à votre ressource Application Insights. Vous pouvez utiliser cette chaîne de recherche pour trouver les données pertinentes :

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    La capture d’écran ci-dessous montre un exemple de deux recherches à partir de deux différentes ressources d’intelligence artificielle. 
    
    * Celle de gauche provient d’une application qui ne reçoit pas de requêtes pendant que le profileur est en cours d’exécution. Vous pouvez voir le message signalant que le chargement a été annulé en raison de l’absence d’activité. 

    * Dans l’exemple de droite, vous pouvez voir que le profileur a démarré et envoyé des événements personnalisés quand il a détecté des requêtes qui ont eu lieu pendant son exécution. Si vous voyez l’événement personnalisé ServiceProfilerSample, cela signifie que le profileur a attaché une trace à une requête, et vous pouvez afficher la trace à partir de la page Performances d’Application Insights.

    * Si aucune donnée de télémétrie n’est visible, le profileur n’est pas en cours d’exécution. Vous devrez peut-être lire les sections de résolution des problèmes correspondant à votre type d’application spécifique ci-dessous.  

     ![Rechercher parmi les données de télémétrie de Profiler][profiler-search-telemetry]

1. Si des requêtes ont eu lieu pendant l’exécution du profileur, vérifiez qu’elles sont gérées par la partie de votre application pour laquelle le profileur est activé. Parfois, les applications sont constituées de plusieurs composants mais Profiler n’est activé que pour certains d’entre eux. La page Configurer Application Insights Profiler montre les composants qui ont chargé des traces.

### <a name="net-core-21-bug"></a>**Bogue lié à .Net Core 2.1** 
Il existe un bogue dans l’agent du profileur qui l’empêche de charger les traces effectuées à partir d’applications s’exécutant sur ASP.NET Core 2.1. Nous travaillons sur un correctif qui sera bientôt disponible. Le correctif pour ce bogue sera déployé d’ici la fin du mois d’octobre.

### <a name="other-things-to-check"></a>**Autres éléments à vérifier :**
* Votre application s’exécute sur .NET Framework 4.6.
* Si votre application web est une application ASP.NET Core, elle doit exécuter au moins ASP.NET Core 2.0.
* Si les données que vous essayez d’afficher datent d’il y a plus de deux semaines, essayez de limiter votre filtre de temps puis réessayez. Les traces sont supprimées après sept jours.
* Vérifiez que votre proxy ou pare-feu n’a pas bloqué l’accès à https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>**Double comptage dans des threads parallèles**

Dans certains cas, la mesure du temps total dans la visionneuse de pile est supérieure à la durée de la requête.

Cette situation peut se présenter lorsque deux threads au moins sont associés à une requête et lorsqu’ils s’exécutent en parallèle. Dans ce cas, le temps total de threads est supérieur au temps écoulé. Un thread peut être en attente le temps que l’autre se termine. La visionneuse tente de détecter cette situation et omet les attentes sans intérêt, mais elle se trompe en affichant trop d’informations plutôt qu’en omettant ce qui pourrait être des informations critiques.

Quand vous voyez des threads parallèles dans vos traces, identifiez les threads en attente pour confirmer le chemin critique pour la requête. Dans la plupart des cas, le thread qui passe rapidement à un état d’attente attend simplement les autres threads. Concentrez-vous sur ces autres threads et ignorez le temps dans les threads en attente.

### <a name="error-report-in-the-profiling-viewer"></a>**Rapport d’erreurs dans la visionneuse de profilage**
Soumettez un ticket de support dans le portail. Veillez à indiquer l’ID de corrélation du message d’erreur.

## <a name="troubleshooting-profiler-on-app-services"></a>Résolution des problèmes liés à Profiler sur App Services
### <a name="for-the-profiler-to-work-properly"></a>**Pour que le profileur fonctionne correctement :**
* Votre plan de service d’application web doit être au niveau De base ou supérieur.
* L’extension Application Insights pour App Services (2.6.5) doit être installée sur votre application web.
* Le paramètre **APPINSIGHTS_INSTRUMENTATIONKEY** de votre application web doit utiliser la même clé d’instrumentation que le SDK Application Insights.
* Le paramètre d’application **APPINSIGHTS_PROFILERFEATURE_VERSION** de votre application web doit être défini et avoir la valeur 1.0.0.
* La tâche web **ApplicationInsightsProfiler2** doit être en cours d’exécution. Vous pouvez vérifier la tâche web en accédant à [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/) et en ouvrant le **Tableau de bord WebJobs** sous le menu Outils. Comme vous pouvez le voir dans les captures d’écran ci-dessous, un clic sur le lien ApplicationInsightsProfiler2 vous permet d’afficher les détails de la tâche web, notamment le journal.

    Voici le lien sur lequel vous devez cliquer pour afficher les détails de la tâche web : 

    ![profiler-webjob]

    Voici la page qui affiche les détails. Vous pouvez télécharger le journal et l’envoyer à notre équipe si vous ne trouvez pas pourquoi le profileur ne fonctionne pas.
    
    ![profiler-webjob-log]

### <a name="manual-installation"></a>**Installation manuelle**

Lorsque vous configurez Profiler, des mises à jour sont appliquées aux paramètres de l’application web. Vous pouvez appliquer les mises à jour manuellement si votre environnement l’exige. Cela peut être le cas, par exemple, si votre application s’exécute dans un environnement d’applications web pour PowerApps.

1. Dans le **volet de contrôle de l’application web**, ouvrez **Paramètres**.
1. Définissez **Version du .NET Framework** sur **4.6**.
1. Activez l’option **Toujours actif**.
1. Ajoutez le paramètre d’application **APPINSIGHTS_INSTRUMENTATIONKEY** et définissez la valeur sur la même clé d’instrumentation que celle utilisée par le kit de développement logiciel (SDK).
1. Ajoutez le paramètre d’application **APPINSIGHTS_PROFILERFEATURE_VERSION** et affectez-lui la valeur 1.0.0.
1. Ouvrez **Outils avancés**.
1. Sélectionnez **Aller à** pour ouvrir le site web Kudu.
1. Sur le site web Kudu, sélectionnez **Extensions de site**.
1. Installez **Application Insights** à partir de la galerie Azure Web Apps.
1. Redémarrez l’application web.

### <a name="too-many-active-profiling-sessions"></a>**Trop de sessions de profilage actives**

Actuellement, vous pouvez activer Profiler sur un maximum de quatre applications web Azure et emplacements de déploiement exécutés sur le même plan de service. Si la tâche web de Profiler signale un nombre trop important de sessions de profilage actives, vous devez déplacer certaines applications web vers un autre plan de service.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erreur de déploiement : Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Si vous redéployez votre application web sur une ressource Web Apps avec Profiler activé, le message suivant peut s’afficher :

*Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs’*

Cette erreur se produit si vous exécutez Web Deploy à partir de scripts ou du pipeline de déploiement Azure DevOps. La solution consiste à ajouter les paramètres de déploiement supplémentaires suivants à la tâche Web Deploy :

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ces paramètres suppriment le dossier utilisé par Application Insights Profiler et débloquent le processus de redéploiement. Ils n’affectent pas l’instance de Profiler qui est en cours d’exécution.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Comment savoir si Application Insights Profiler est en cours d’exécution ?

Profiler s’exécute comme une tâche web en continu dans l’application web. Vous pouvez ouvrir la ressource de l’application web dans le [portail Azure](https://portal.azure.com). Dans le volet **WebJobs**, vérifiez l’état de **ApplicationInsightsProfiler**. S’il n’est pas en cours d’exécution, ouvrez les **Journaux** pour en savoir plus.

## <a name="troubleshooting-problems-with-profiler-and-wad"></a>Résolution des problèmes liés à Profiler et à WAD (Diagnostics Windows Azure)

Il existe trois points à vérifier pour savoir si le profileur est configuré correctement par WAD. Tout d’abord, vous devez vérifier que le contenu de la configuration WAD qui est déployé est celui auquel vous vous attendez. Ensuite, vous devez vérifier que WAD transmet l’iKey appropriée sur la ligne de commande du profileur. Pour finir, vous pouvez consulter le fichier journal du profileur pour vérifier s’il s’est exécuté mais a reçu une erreur. 

Pour vérifier les paramètres qui ont été utilisés pour configurer WAD, vous devez vous connecter à la machine virtuelle et ouvrir le fichier journal à cet emplacement : 
```
c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.logs  
```
Dans ce fichier, vous pouvez rechercher la chaîne « WadCfg » et vous trouverez les paramètres qui ont été passés à la machine virtuelle pour configurer WAD. Vous pouvez vérifier que l’iKey utilisée par le récepteur du profileur est correcte.

Ensuite, vous pouvez vérifier la ligne de commande utilisée pour démarrer le profileur. Le fichier suivant contient les arguments utilisés pour lancer le profileur.
```
D:\ProgramData\ApplicationInsightsProfiler\config.json
```
Vérifiez que l’ikey sur la ligne de commande du profileur est correcte. 

Troisièmement, à l’aide du chemin trouvé dans le fichier config.json ci-dessus, vérifiez le fichier journal du profileur. Il contient des informations de débogage indiquant les paramètres utilisés par le profileur ainsi que des messages d’état et d’erreur provenant du profileur. Si le profileur s’exécute pendant que votre application reçoit des requêtes, vous verrez ce message : Activity detected from iKey. Lors du chargement de la trace, vous verrez ce message : Start to upload trace. 


[profiler-search-telemetry]:./media/app-insights-profiler/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/app-insights-profiler/Profiler-webjob.png
[profiler-webjob-log]:./media/app-insights-profiler/Profiler-webjob-log.png








