---
title: Résoudre les problèmes liés à Azure Application Insights Profiler
description: Cet article décrit les étapes de dépannage et donne des informations afin d’aider les développeurs qui rencontrent des difficultés à activer ou utiliser Application Insights Profiler.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6022bf975352f9f70c4ba8aa716a695ead590a32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432380"
---
# <a name="troubleshoot-problems-enabling-or-viewing-application-insights-profiler"></a>Résoudre les problèmes d’activation ou d’affichage d’Application Insights Profiler

## <a id="troubleshooting"></a>Résolution de problèmes générale

### <a name="profiles-are-uploaded-only-if-there-are-requests-to-your-application-while-profiler-is-running"></a>Les profils sont chargés uniquement si des demandes sont envoyées à votre application quand Profiler est en cours d’exécution

Azure Application Insights Profiler collecte les données de profilage pendant deux minutes toutes les heures. Il collecte également les données quand vous sélectionnez le bouton **Profiler maintenant** situé dans le volet **Configurer Application Insights Profiler**. Toutefois, les données de profilage sont chargées uniquement quand elles peuvent être jointes à une demande envoyée pendant l’exécution de Profiler. 

Profiler écrit les messages de trace et les événements personnalisés dans votre ressource Application Insights. Vous pouvez utiliser ces événements pour voir comment Profiler s’exécute. Si vous pensez que Profiler devrait être en cours d’exécution et capturer des traces, mais que vous ne les voyez pas dans le volet **Performances**, vous pouvez vérifier comment Profiler s’exécute :

1. Recherchez des messages de trace et des événements personnalisés envoyés par Profiler à votre ressource Application Insights. Vous pouvez utiliser cette chaîne de recherche pour trouver les données pertinentes :

    ```
    stopprofiler OR startprofiler OR upload OR ServiceProfilerSample
    ```
    L’image suivante affiche deux exemples de recherches à partir de deux ressources AI : 
    
   * À gauche, l’application ne reçoit pas de demandes pendant l’exécution de Profiler. Le message indique que le chargement a été annulé en raison de l’absence d’activité. 

   * À droite, Profiler a démarré et a envoyé des événements personnalisés quand il a détecté des demandes envoyées pendant son exécution. Si vous voyez l’événement personnalisé ServiceProfilerSample, cela signifie que Profiler a attaché une trace à une demande et vous pouvez voir la trace dans le volet **Performances d’Application Insights**.

     Si aucune donnée de télémétrie ne s’affiche, Profiler n’est pas exécuté. Pour résoudre les problèmes, consultez les sections de résolution des problèmes pour votre type d’application plus loin dans cet article.  

     ![Rechercher dans les données de télémétrie de Profiler][profiler-search-telemetry]

1. Si des demandes ont été envoyées pendant l’exécution de Profiler, vérifiez qu’elles sont traitées par la partie de votre application pour laquelle Profiler est activé. Parfois, les applications sont constituées de plusieurs composants et Profiler peut être activé pour certains d’entre eux uniquement. Le volet **Configurer Application Insights Profiler** montre les composants qui ont chargé des traces.

### <a name="other-things-to-check"></a>Autres éléments à vérifier
* Vérifiez que votre application web s’exécute sur .Net Framework 4.6.
* Si votre application web est une application ASP.NET Core, elle doit exécuter au moins ASP.NET Core 2.0.
* Si les données que vous essayez de voir datent de plus de deux semaines, essayez de limiter votre filtre de temps et réessayez. Les traces sont supprimées après sept jours.
* Vérifiez que des proxys ou un pare-feu ne bloquent pas l’accès à https://gateway.azureserviceprofiler.net.

### <a id="double-counting"></a>Double comptage dans des threads parallèles

Dans certains cas, la mesure du temps total dans la visionneuse de pile est supérieure à la durée de la requête.

Cette situation peut se présenter quand deux threads au moins sont associés à une demande et qu’ils s’exécutent en parallèle. Dans ce cas, le temps total de threads est supérieur au temps écoulé. Un thread peut être en attente le temps que l’autre se termine. La visionneuse tente de détecter cette situation et omet l’attente sans intérêt. Ce faisant, elle se trompe en affichant trop d’informations plutôt qu’en omettant ce qui pourrait être des informations critiques.

Quand vous voyez des threads parallèles dans vos traces, identifiez les threads en attente pour confirmer le chemin critique de la demande. Généralement, le thread qui passe rapidement à un état d’attente attend simplement les autres threads. Concentrez-vous sur ces autres threads et ignorez le temps dans les threads en attente.

### <a name="error-report-in-the-profile-viewer"></a>Rapport d’erreurs dans la visionneuse du profil
Soumettez un ticket de support dans le portail. Veillez à indiquer l’ID de corrélation du message d’erreur.

## <a name="troubleshoot-profiler-on-azure-app-service"></a>Résoudre les problèmes de Profiler dans Azure App Service
Pour que Profiler fonctionne correctement :
* Votre plan de service d’application web doit être au niveau De base ou supérieur.
* Votre application web doit avoir Application Insights activé.
* Votre application web doit avoir les paramètres d’application suivants :

    |Paramètre d'application    | Valeur    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pour votre ressource Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |


* La tâche web **ApplicationInsightsProfiler3** doit être en cours d’exécution. Pour vérifier le tâche web :
   1. Accédez à [Kudu](https://blogs.msdn.microsoft.com/cdndevs/2015/04/01/the-kudu-debug-console-azure-websites-best-kept-secret/).
   1. Dans le menu **Outils**, sélectionnez **Tableau de bord WebJobs**.  
      Le volet **WebJobs** s’ouvre. 
   
      ![profiler-webjob]   
   
   1. Pour voir les détails de la tâche web, notamment le journal, sélectionnez le lien **ApplicationInsightsProfiler3**.  
     Le volet **Détails de la tâche web continue** s’ouvre.

      ![profiler-webjob-log]

Si vous ne pouvez pas déterminer pourquoi Profiler ne fonctionne pas, téléchargez le journal et envoyez-le à notre équipe pour obtenir de l’aide, serviceprofilerhelp@microsoft.com. 
    
### <a name="manual-installation"></a>Installation manuelle

Lorsque vous configurez Profiler, des mises à jour sont appliquées aux paramètres de l’application web. Si votre environnement l’exige, vous pouvez appliquer les mises à jour manuellement. Cela peut être le cas, par exemple, si votre application s’exécute dans un environnement d’applications web pour PowerApps. Pour appliquer manuellement les mises à jour :

1. Dans le **volet de contrôle de l’application web**, ouvrez **Paramètres**.

1. Définissez **Version du .NET Framework** sur **4.6**.

1. Activez l’option **Toujours actif**.
1. Créez les paramètres d’application suivants :

    |Paramètre d'application    | Valeur    |
    |---------------|----------|
    |APPINSIGHTS_INSTRUMENTATIONKEY         | iKey pour votre ressource Application Insights    |
    |APPINSIGHTS_PROFILERFEATURE_VERSION | 1.0.0 |
    |DiagnosticServices_EXTENSION_VERSION | ~3 |

### <a name="too-many-active-profiling-sessions"></a>Trop de sessions de profilage actives

Actuellement, vous pouvez activer Profiler sur un maximum de quatre applications web Azure et emplacements de déploiement exécutés sur le même plan de service. Si vous avez plus de quatre applications web exécutées dans un seul plan App Service, Profiler peut lever l’exception *Microsoft.ServiceProfiler.Exceptions.TooManyETWSessionException*. Profiler s’exécute séparément pour chaque application web et tente de démarrer une session de suivi d’événements pour Windows (ETW) pour chaque application. Toutefois, seul un nombre limité de sessions ETW peuvent être actives en même temps. Si la tâche web de Profiler signale trop de sessions de profilage actives, déplacez des applications web vers un autre plan de service.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootapp_datajobs"></a>Erreur de déploiement : Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Si vous redéployez votre application web sur une ressource Web Apps avec Profiler activé, vous pouvez voir le message suivant :

*Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs’*

Cette erreur se produit si vous exécutez Web Deploy à partir de scripts ou du pipeline de déploiement Azure DevOps. La solution consiste à ajouter les paramètres de déploiement supplémentaires suivants à la tâche Web Deploy :

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ces paramètres suppriment le dossier utilisé par Application Insights Profiler et débloquent le processus de redéploiement. Ils n’affectent pas l’instance de Profiler qui est en cours d’exécution.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Comment savoir si Application Insights Profiler est en cours d’exécution ?

Profiler s’exécute comme une tâche web continue dans l’application web. Vous pouvez ouvrir la ressource de l’application web dans le [portail Azure](https://portal.azure.com). Dans le volet **WebJobs**, vérifiez l’état de **ApplicationInsightsProfiler**. S’il n’est pas en cours d’exécution, ouvrez les **Journaux d’activité** pour en savoir plus.

## <a name="troubleshoot-problems-with-profiler-and-azure-diagnostics"></a>Résoudre les problèmes de Profiler et de Diagnostics Azure

>**Le bogue du profileur fourni avec WAD pour les Services cloud a été corrigé.** La dernière version de WAD (1.12.2.0) pour les Services cloud fonctionne avec toutes les versions récentes du kit de développement logiciel (SDK) App Insights. Les hôtes des Services cloud mettent automatiquement à niveau WAD, mais ce n'est pas immédiat. Pour forcer une mise à niveau, vous pouvez redéployer votre service ou redémarrer le nœud.

Pour voir si Profiler est correctement configuré par Diagnostics Azure, effectuez les trois actions suivantes : 
1. Premièrement, vérifiez si le contenu de la configuration Diagnostics Azure qui est déployé est celui que vous attendez. 

1. Deuxièmement, vérifiez que Diagnostics Azure passe l’iKey appropriée sur la ligne de commande de Profiler. 

1. Troisièmement, consultez le fichier journal de Profiler pour voir s’il s’est exécuté et a retourné une erreur. 

Pour vérifier les paramètres qui ont été utilisés pour configurer Diagnostics Azure :

1. Connectez-vous à la machine virtuelle, puis ouvrez le fichier journal à cet emplacement. (Le lecteur peut être c: ou d:, et la version du plug-in peut être différente.)

    ```
    c:\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log  
    ```
    or
    ```
    c:\WindowsAzure\logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.11.3.12\DiagnosticsPlugin.log
    ```

1. Dans ce fichier, vous pouvez rechercher la chaîne **WadCfg** pour voir les paramètres qui ont été passés à la machine virtuelle pour configurer Diagnostics Azure. Vous pouvez vérifier si l’iKey utilisée par le récepteur de Profiler est correcte.

1. Vérifiez la ligne de commande qui permet de démarrer Profiler. Les arguments qui sont utilisés pour lancer Profiler se trouvent dans le fichier suivant. (Le lecteur peut être c: ou d:)

    ```
    D:\ProgramData\ApplicationInsightsProfiler\config.json
    ```

1. Vérifiez que l’ikey sur la ligne de commande de Profiler est correcte. 

1. À l’aide du chemin trouvé dans le fichier *config.json* précédent, consultez le fichier journal de Profiler. Il affiche les informations de débogage qui indiquent les paramètres utilisés par Profiler. Il affiche également des messages d’état et d’erreur de Profiler.  

    Si Profiler s’exécute pendant que votre application reçoit des demandes, vous voyez ce message : *Activité de l’iKey détectée*. 

    Quand la trace est en cours de chargement, le message suivant s’affiche : *Démarrage du chargement de la trace*. 


## <a name="edit-network-proxy-or-firewall-rules"></a>Modifier les règles de pare-feu ou de proxy réseau

Si votre application se connecte à Internet via un proxy ou un pare-feu, il se peut que vous deviez modifier les règles pour autoriser votre application à communiquer avec le service Application Insights Profiler. Les adresses IP utilisées par Application Insights Profiler sont incluses dans la balise de service Azure Monitor.


[profiler-search-telemetry]:./media/profiler-troubleshooting/Profiler-Search-Telemetry.png
[profiler-webjob]:./media/profiler-troubleshooting/Profiler-webjob.png
[profiler-webjob-log]:./media/profiler-troubleshooting/Profiler-webjob-log.png








