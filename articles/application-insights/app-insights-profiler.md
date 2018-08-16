---
title: Profiler des applications web dynamiques sur Azure avec Application Insights Profiler | Microsoft Docs
description: Identifiez le chemin réactif dans le code de votre serveur web avec un profileur de faible encombrement.
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
ms.openlocfilehash: 6048a17bf50ecac691c7cf687f87e454c54ee9d9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521881"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profiler des applications web dynamiques sur Azure avec Application Insights

Cette fonctionnalité d’Azure Application Insights est généralement disponible pour la fonctionnalité Web Apps d’Azure App Service et est en préversion pour les ressources de calcul Azure. Pour plus d’informations concernant [l’utilisation locale du profileur](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).

Cet article traite du temps passé dans chaque méthode de votre application web dynamique lorsque vous utilisez [Application Insights](app-insights-overview.md). L’outil Application Insights Profiler affiche les profils détaillés des requêtes dynamiques qui ont été prises en charge par votre application. Profiler met en évidence le *chemin réactif* qui utilise le plus de temps. Les requêtes avec différents temps de réponse sont profilées par échantillonnage. À l’aide de diverses techniques, vous pouvez réduire la charge qui est associée à l’application.

Profiler fonctionne actuellement pour les applications web ASP.NET et ASP.NET Core s’exécutant sur Web Apps. L’utilisation de Profiler nécessite au minimum le niveau de service De base.

## <a id="installation"></a> Activer Profiler pour votre application Web Apps

Une fois que vous avez déployé une application web, que vous ayez ou non inclus le SDK Application Insights dans le code source, procédez comme suit :

1. Accédez au volet **App Services** dans le portail Azure.
1. Accédez au volet **Paramètres > Surveillance**.

   ![Activer App Insights sur le portail App Services](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Suivez les instructions dans le volet pour créer une nouvelle ressource, ou sélectionnez une ressource Application Insights existante pour surveiller votre application web. Acceptez toutes les options par défaut. **Diagnostics de niveau code** est activé par défaut et active Profiler.

   ![Ajouter l’extension de site Application Insights][Enablement UI]

1. Profiler est maintenant installé avec l’extension de site Application Insights et il est activé avec un paramètre d’application App Services.

    ![Paramètre d’application pour Profiler][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Activer Profiler pour les ressources de calcul Azure (version préliminaire)

Pour plus d’informations, consultez la [version préliminaire de Profiler pour les ressources de calcul Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Afficher les données du profileur

Vérifiez que votre application reçoit le trafic. Si vous faites une expérience, vous pouvez générer des requêtes pour votre application web à l’aide des [tests de performances Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Si vous venez d’activer Profiler, vous pouvez exécuter un bref test de charge durant environ 15 minutes, ce qui doit générer des traces du profileur. Si Profiler est déjà activé depuis un moment, notez qu’il s’exécute de manière aléatoire deux fois par heure pendant une durée de deux minutes. Nous vous suggérons d’exécuter le test de charge sur une durée d’une heure pour vous assurer d’obtenir des échantillons de traces du profileur.

Une fois que votre application reçoit du trafic, accédez au volet **Performances**, sélectionnez **Prendre des mesures** pour afficher les traces du profileur, puis sélectionnez le bouton **Traces du profileur**.

![Aperçu du volet de performances Application Insights - Traces du profileur][performance-blade-v2-examples]

Sélectionnez un exemple pour afficher, au niveau du code, une répartition du temps d’exécution de la requête.

![Explorateur de trace d’Application Insights][trace-explorer]

L’Explorateur de trace affiche les informations suivantes :

* **Afficher le chemin réactif** : ouvre le plus grand nœud feuille, ou du moins un élément qui s’en rapproche. Dans la plupart des cas, ce nœud est adjacent à un goulot d’étranglement.
* **Étiquette** : nom de la fonction ou de l’événement. L’arborescence affiche une combinaison des codes et des événements qui se sont produits (par exemple, des événements SQL et HTTP). L’événement supérieur représente la durée globale de la requête.
* **Écoulé** : intervalle de temps entre le début de l’opération et la fin.
* **Quand** : moment auquel la fonction ou l’événement était en cours d’exécution par rapport à d’autres fonctions.

## <a name="how-to-read-performance-data"></a>Comment lire les données de performances

Le profileur de service Microsoft utilise un ensemble de méthodes d’échantillonnage et une instrumentation pour analyser les performances de votre application. Durant la collecte détaillée, le profileur de service échantillonne le pointeur d’instruction de chaque processeur de l’ordinateur à chaque milliseconde. Chaque échantillon capture l’ensemble de la pile des appels du thread en cours d’exécution. Il fournit des informations détaillées sur l’activité de ce thread, à un niveau d’abstraction élevé et faible. Le profileur de service collecte également d’autres événements afin de suivre la corrélation et la causalité des activités, tels que les événements de changement de contexte, les événements TPL (Task Parallel Library) et les événements de pool de threads.

La pile des appels présentée dans l’affichage chronologique est le résultat de l’échantillonnage et de l’instrumentation. Étant donné que chaque échantillon capture l’ensemble de la pile des appels du thread, il inclut le code de Microsoft .NET Framework, et des autres infrastructures que vous référencez.

### <a id="jitnewobj"></a>Allocation d’objets (clr!JIT\_Nouveau ou clr!JIT\_Newarr1)

**clr!JIT\_Nouveau** et **clr!JIT\_Newarr1** sont des fonctions d’assistance du framework .NET qui allouent la mémoire à partir d’un tas managé. **clr!JIT\_Nouveau** est appelé lorsqu’un objet est alloué. **clr!JIT\_Newarr1** est appelé lorsqu’un tableau d’objet est alloué. Ces deux fonctions sont généralement rapides et s’exécutent en relativement peu de temps. Si vous constatez que **clr!JIT\_New** ou **clr!JIT\_Newarr1** prend un certain temps dans votre scénario, cela indique que le code alloue peut-être de nombreux objets et qu’il consomme une quantité importante de mémoire.

### <a id="theprestub"></a>Code de chargement (clr!ThePreStub)

**clr!ThePreStub** est une fonction d’assistance intégrée au framework .NET qui prépare le code en vue de sa première exécution. Elle implique généralement au moins une compilation JIT (juste à temps). Pour chaque méthode C#, **clr!ThePreStub** doit être appelé au maximum une fois pendant la durée de vie d’un processus.

Si **clr!ThePreStub** prend un certain temps pour traiter une requête, cela indique que la requête est la première à exécuter cette méthode. Le runtime du .NET Framework a besoin de temps pour charger la première méthode. Vous pouvez envisager d’utiliser un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent, ou encore envisager d’exécuter Native Image Generator (ngen.exe) sur vos assemblys.

### <a id="lockcontention"></a>Contention de verrouillage (clr!JITutil\_MonContention or clr!JITutil\_MonEnterWorker)

**clr!JITutil\_MonContention** ou **clr!JITutil\_MonEnterWorker** indique que le thread actuel est en attente d’ouverture d’un verrou. Ce texte est souvent affiché lors de l’exécution d’une instruction **LOCK** C#, lors de l’appel de la méthode **Monitor.Enter** ou lors de l’appel d’une méthode avec l’attribut **MethodImplOptions.Synchronized**. La contention de verrouillage se produit généralement lorsque le thread _A_ acquiert un verrou et que le thread _B_ tente d’acquérir le même verrou avant qu’il ne soit libéré par le thread _A_.

### <a id="ngencold"></a>Code de chargement ([COLD])

Si le nom de la méthode contient **[COLD]**, par exemple **mscorlib.ni![COLD]System.Reflection.CustomAttribute.IsDefined**, cela signifie que le runtime du framework .NET exécute pour la première fois du code qui n’utilise pas une <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">optimisation guidée par profil</a>. Pour chaque méthode, il doit s’afficher au maximum une fois pendant la durée de vie du processus.

Si le code de chargement prend beaucoup de temps pour traiter une requête, cela indique que la requête est la première à exécuter la partie non optimisée de la méthode. Vous pouvez envisager d’utiliser un processus de mise en route qui exécute cette partie du code avant que vos utilisateurs y accèdent.

### <a id="httpclientsend"></a>Envoyer une requête HTTP

Les méthodes telles que **HttpClient.Send** indiquent que le code attend l’exécution d’une requête HTTP.

### <a id="sqlcommand"></a>Opération de base de données

Une méthode telle que **SqlCommand.Execute** indique que le code attend l’exécution d’une opération de base de données.

### <a id="await"></a>En attente (AWAIT\_TIME)

**AWAIT\_TIME** indique que le code attend la fin de l’exécution d’une autre tâche. Cela se produit généralement avec l’instruction C# **AWAIT**. Lorsque le code exécute une instruction C# **AWAIT**, le thread se déroule et redonne le contrôle au pool de threads : aucun thread n’est bloqué en attendant la fin de l’exécution de l’instruction **AWAIT**. Toutefois, le thread qui a exécuté l’instruction **AWAIT** est logiquement « bloqué » en attendant que l’opération se termine. L’instruction **AWAIT\_TIME** indique le temps de blocage en attendant l’exécution de la tâche.

### <a id="block"></a>Temps de blocage

**BLOCKED_TIME** indique que le code attend qu’une autre ressource soit disponible. Il peut, par exemple, attendre un objet de synchronisation, la disponibilité d’un thread ou la fin d’une requête.

### <a id="cpu"></a>Temps processeur

Le processeur est occupé à exécuter les instructions.

### <a id="disk"></a>Temps du disque

L’application effectue les opérations de disque.

### <a id="network"></a>Temps réseau

L’application effectue les opérations réseau.

### <a id="when"></a>Colonne « When » (Quand)

La colonne **When** permet de voir comment les exemples INCLUSIFS collectés pour un nœud varient au fil du temps. La plage totale de la requête est divisée en 32 périodes. Les exemples inclusifs de ce nœud sont regroupés dans ces 32 périodes. Chaque période est représentée sous forme de barre. La hauteur de la barre représente une valeur à l’échelle. Pour les nœuds marqués **CPU_TIME** ou **BLOCKED_TIME**, ou lorsqu’il existe une relation évidente de consommation d’une ressource (par exemple, processeur, disque, thread), la barre représente la consommation d’une des ressources au cours de cette période. Pour ces mesures, il est possible d’obtenir une valeur supérieure à 100 % en consommant plusieurs ressources. Par exemple, si vous utilisez en moyenne deux processeurs sur un intervalle donné, vous obtenez 200 %.

## <a name="limitations"></a>Limites

Par défaut, la période de conservation des données est de 5 jours. La quantité maximale de données ingérées par jour est de 10 Go.

L’utilisation du service Profiler est gratuite. Pour pourvoir utiliser le service Profiler, votre application web doit être hébergée au moins au niveau De base de Web Apps.

## <a name="overhead-and-sampling-algorithm"></a>Surcharge et algorithme d’échantillonnage

Profiler s’exécute aléatoirement pendant 2 minutes toutes les heures sur chaque machine virtuelle qui héberge l’application sur laquelle Profiler est activé pour capturer des traces. Lorsque Profiler est en cours d’exécution, il ajoute entre 5 et 15 % de surcharge pour les processeurs sur le serveur.

Plus le nombre de serveurs disponibles pour héberger l’application est important, moins Profiler a d’impact sur les performances globales de l’application. Cela est dû au fait qu’en raison de l’algorithme d’échantillonnage, Profiler s’exécute à tout moment sur seulement 5 % des serveurs. Davantage de serveurs sont disponibles pour traiter les requêtes web afin de compenser la surcharge du serveur provoquée par l’exécution de Profiler.

## <a name="disable-profiler"></a>Désactiver Profiler

Pour arrêter ou redémarrer Profiler pour une instance d’application web individuelle, accédez à la ressource Web Apps sous **Tâches web**. Pour supprimer Profiler, accédez à **Extensions**.

![Désactiver Profiler pour une tâche web][disable-profiler-webjob]

Nous vous recommandons d’activer, dès que possible, Profiler sur toutes vos applications web afin de découvrir d’éventuels problèmes de performance.

Si vous utilisez WebDeploy pour déployer des modifications sur votre application web, veillez à ne pas supprimer le dossier App_Data lors du déploiement. Dans le cas contraire, les fichiers de l’extension de Profiler seront supprimés la prochaine fois que vous déploierez l’application web dans Azure.


## <a id="troubleshooting"></a>Résolution des problèmes

### <a name="too-many-active-profiling-sessions"></a>Trop de sessions de profilage actives

Actuellement, vous pouvez activer Profiler sur un maximum de quatre applications web Azure et emplacements de déploiement exécutés sur le même plan de service. Si la tâche web de Profiler signale un nombre trop important de sessions de profilage actives, vous devez déplacer certaines applications web vers un autre plan de service.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Comment savoir si Application Insights Profiler est en cours d’exécution ?

Profiler s’exécute comme une tâche web en continu dans l’application web. Vous pouvez ouvrir la ressource de l’application web dans le [portail Azure](https://portal.azure.com). Dans le volet **WebJobs**, vérifiez l’état de **ApplicationInsightsProfiler**. S’il n’est pas en cours d’exécution, ouvrez les **Journaux** pour en savoir plus.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Pourquoi aucun exemple de pile ne s’affiche même si Profiler est en cours d’exécution ?

Vous pouvez vérifier plusieurs points :

* Assurez-vous que votre plan de service d’application web est au niveau De base ou niveau supérieur.
* Assurez-vous que le SDK Application Insights 2.2 Bêta ou version ultérieure est activé sur votre application web.
* Vérifiez dans votre application web que le paramètre **APPINSIGHTS_INSTRUMENTATIONKEY** utilise la même clé d’instrumentation que celle utilisée par le SDK Application Insights.
* Assurez-vous que votre application web s’exécute sur .Net Framework 4.6.
* Si votre application web est une application ASP.NET Core, elle doit exécuter au moins ASP.NET Core 2.0.

Après le démarrage de Profiler, vous devez vous attendre à une courte période de mise en route au cours de laquelle Profiler collecte activement plusieurs traces de performances. Après cela, Profiler collecte des traces de performances pendant deux minutes, toutes les heures.

> [!NOTE]
> Il existe un bogue dans l’agent du profileur qui l’empêche de charger les traces effectuées à partir d’applications s’exécutant sur ASP.NET Core 2.1. Nous travaillons sur un correctif qui sera bientôt disponible.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>J’avais l’habitude d’utiliser Azure Service Profiler. Qu’est-il devenu ?

Lorsque vous activez Application Insights Profiler, l’agent Azure Service Profiler est désactivé.

### <a id="double-counting"></a>Double comptage dans des threads parallèles

Dans certains cas, la mesure du temps total dans la visionneuse de pile est supérieure à la durée de la requête.

Cette situation peut se présenter lorsque deux threads au moins sont associés à une requête et lorsqu’ils s’exécutent en parallèle. Dans ce cas, le temps total de threads est supérieur au temps écoulé. Un thread peut être en attente le temps que l’autre se termine. La visionneuse tente de détecter et d’omettre les attentes sans intérêt, mais se trompe en affichant trop d’informations plutôt qu’en omettant ce qui pourrait être des informations critiques.

Quand vous voyez des threads parallèles dans vos traces, identifiez les threads en attente pour confirmer le chemin critique pour la requête. Dans la plupart des cas, le thread qui passe rapidement à un état d’attente attend simplement les autres threads. Concentrez-vous sur ces autres threads et ignorez le temps dans les threads en attente.

### <a id="issue-loading-trace-in-viewer"></a>Aucune donnée de profilage

Vous pouvez vérifier plusieurs points :

* Si les données que vous essayez d’afficher datent d’il y a plus de deux semaines, essayez de limiter votre filtre de temps puis réessayez.
* Vérifiez que votre proxy ou pare-feu n’a pas bloqué l’accès à https://gateway.azureserviceprofiler.net.
* Vérifiez que la clé d’instrumentation Application Insights que vous utilisez dans votre application est identique à celle de la ressource Application Insights que vous avez utilisée pour activer le profilage. La clé se trouve généralement dans le fichier ApplicationInsights.config, mais peut également se trouver dans le fichier web.config ou app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Rapport d’erreurs dans la visionneuse de profilage

Soumettez un ticket de support dans le portail. Veillez à indiquer l’ID de corrélation du message d’erreur.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Erreur de déploiement : Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs'

Si vous redéployez votre application web vers une ressource Web Apps avec Profiler activé, un message de ce type peut s’afficher :

*Répertoire non vide 'D:\\home\\site\\wwwroot\\App_Data\\jobs’*

Cette erreur se produit si vous exécutez Web Deploy à partir de scripts ou du pipeline de déploiement Visual Studio Team Services. La solution consiste à ajouter les paramètres de déploiement supplémentaires suivants à la tâche Web Deploy :

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Ces paramètres suppriment le dossier utilisé par Application Insights Profiler et débloquent le processus de redéploiement. Ils n’affectent pas l’instance de Profiler qui est en cours d’exécution.

## <a name="manual-installation"></a>Installation manuelle

Lorsque vous configurez Profiler, des mises à jour sont appliquées aux paramètres de l’application web. Vous pouvez appliquer les mises à jour manuellement si votre environnement l’exige. Cela peut être le cas, par exemple, si votre application s’exécute dans un environnement d’applications web pour PowerApps.

1. Dans le **volet de contrôle de l’application web**, ouvrez **Paramètres**.
1. Définissez **Version du .NET Framework** sur **4.6**.
1. Activez l’option **Toujours actif**.
1. Ajoutez le paramètre d’application **APPINSIGHTS_INSTRUMENTATIONKEY** et définissez la valeur sur la même clé d’instrumentation que celle utilisée par le kit de développement logiciel (SDK).
1. Ouvrez **Outils avancés**.
1. Sélectionnez **Aller à** pour ouvrir le site web Kudu.
1. Sur le site web Kudu, sélectionnez **Extensions de site**.
1. Installez **Application Insights** à partir de la galerie Azure Web Apps.
1. Redémarrez l’application web.

## <a id="profileondemand"></a> Déclencher manuellement Profiler

Vous pouvez déclencher Profiler manuellement en un clic de bouton. Supposons que vous exécutez un test de performances web. Vous avez besoin de traces pour vous aider à comprendre comment votre application web fonctionne avec une certaine charge. Pouvoir contrôler le moment où les traces sont capturées est essentiel, car si vous savez quand le test de charge s’exécute, l’intervalle d’échantillonnage peut néanmoins ne pas correspondre à ce moment.
Les étapes suivantes montrent comment ce scénario fonctionne :

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Facultatif) Étape 1 : Générer du trafic vers votre application web en démarrant un test de performances web

Si votre application web a déjà du trafic entrant ou si vous voulez simplement générer du trafic manuellement, ignorez cette section et passez à l’étape 2.

Accédez au portail Application Insights, **Configurer > Test des performances**. Cliquez sur le bouton Nouveau pour démarrer un nouveau test de performances.
![Créer un test de performances][create-performance-test]

Dans le volet **Nouveau test de performance**, configurez l’URL cible du test. Acceptez tous les paramètres par défaut et démarrez l’exécution du test de charge.

![Configurer le test de charge][configure-performance-test]

Vous voyez que le nouveau test est d’abord placé en file d’attente, avec un état En cours d’exécution.

![Le test de charge est envoyé et placé en file d’attente][load-test-queued]

![Le test de charge est en cours d’exécution][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Étape 2 : Démarrer Profiler à la demande

Une fois que le test de charge est en cours d’exécution, nous pouvons démarrer Profiler pour capturer des traces sur l’application web pendant qu’elle reçoit la charge.
Accédez au volet Configurer Profiler :

![Entrée du volet Configurer Profiler][configure-profiler-entry]

Dans le **volet Configurer Profiler**, un bouton **Profiler maintenant** permet de déclencher Profiler sur toutes les instances des applications web liées. Vous disposez aussi d’une visibilité sur les moments où Profiler s’est exécuté.

![Profiler à la demande][profiler-on-demand]

Vous verrez une notification et un changement de l’état d’exécution de Profiler.

### <a name="step-3-view-traces"></a>Étape 3 : Afficher les traces

Une fois que l’exécution de Profiler est terminée, suivez les instructions de la notification pour accéder au volet Performance et voir les traces.

### <a name="troubleshooting-on-demand-profiler"></a>Résolution des problèmes de Profiler à la demande

Vous voyez parfois un message d’erreur de délai d’expiration de Profiler après une session à la demande :

![Erreur de délai d’expiration de Profiler][profiler-timeout]

Vous voyez cette erreur pour deux raisons possibles :

1. La session Profiler à la demande a réussi, mais Application Insights a pris plus de temps pour traiter les données collectées. Si les données n’ont pas été entièrement traitées dans les 15 minutes, le portail affiche un message de dépassement du délai d’expiration. Après un certain temps, les traces de Profiler sont néanmoins affichées. Si cela se produit, ignorez simplement le message d’erreur pour l’instant. Nous travaillons activement sur un correctif.

1. Votre application web a une version plus ancienne de l’agent Profiler, qui n’a pas la fonctionnalité « à la demande ». Si vous avez déjà activé Application Insights Profiler, il peut être nécessaire de mettre à jour votre agent Profiler pour commencer à utiliser la fonctionnalité « à la demande ».
  
Suivez ces étapes pour vérifier et installer la version la plus récente de Profiler :

1. Accédez aux paramètres d’application App Services et vérifiez si les paramètres suivants sont définis :
    * **APPINSIGHTS_INSTRUMENTATIONKEY** : Remplacez ceci par la clé d’instrumentation correcte pour Application Insights.
    * **APPINSIGHTS_PORTALINFO** : ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION** : 1.0.0. Si ces paramètres ne sont pas définis, accédez au volet d’activation d’Application Insights pour installer la dernière extension de site.

1. Accédez au volet Application Insights dans le portail App Services.

    ![Activer Application Insights dans le portail App Services][enable-app-insights]

1. Si vous voyez un bouton « Mettre à jour » dans la page suivante, cliquez sur celui-ci pour mettre à jour l’extension de site Application Insights qui va installer le dernier agent Profiler.
![Mettre à jour l’extension de site][update-site-extension]

1. Cliquez ensuite sur **Modifier** pour activer Profiler et sélectionnez **OK** pour enregistrer les modifications.

    ![Modifier et enregistrer Application Insights][change-and-save-appinsights]

1. Revenez à l’onglet **Paramètres de l’application** pour App Service pour vérifier que les éléments des paramètres d’application suivants sont bien définis :
    * **APPINSIGHTS_INSTRUMENTATIONKEY** : Remplacez ceci par la clé d’instrumentation correcte pour Application Insights.
    * **APPINSIGHTS_PORTALINFO** : ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION** : 1.0.0

    ![Paramètres d’application pour Profiler][app-settings-for-profiler]

1. Si vous le souhaitez, vérifiez la version de l’extension et qu’aucune mise à jour n’est disponible.

    ![Rechercher une mise à jour de l’extension][check-for-extension-update]

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation d’Application Insights dans Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
