---
title: Surveillez l'intégrité et l'utilisation de votre application avec Application Insights
description: Prise en main d'Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications.
ms.topic: conceptual
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: d624286d214a86364fe85192bf5ede885d4b6a78
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323449"
---
# <a name="monitor-performance-in-web-applications"></a>Analyse des performances dans les applications web


Assurez-vous que votre application fonctionne correctement et identifiez rapidement toutes les défaillances éventuelles. [Application Insights][start] signale tous les problèmes de performances et toutes les exceptions, et aide à trouver et à diagnostiquer les causes racines.

Application Insights peut surveiller les services WCF, ainsi que les applications et services web Java et ASP.NET. Ils peuvent être hébergés localement, sur des machines virtuelles, ou en tant que sites web Microsoft Azure. 

Côté client, Application Insights peut récupérer les données télémétriques des pages web et d’un large éventail d’appareils, notamment des applications iOS, Android et Windows Store.

## <a name="set-up-performance-monitoring"></a><a name="setup"></a>Configurer la surveillance des performances
Si vous n'avez pas encore ajouté Application Insights à votre projet (il n'inclut pas ApplicationInsights.config), sélectionnez l'une des options suivantes pour commencer :

* [Applications web ASP.NET](./asp-net.md)
  * [Ajout de la surveillance des exceptions](./asp-net-exceptions.md)
  * [Ajout de la surveillance des dépendances](./monitor-performance-live-website-now.md)
* [Applications web Java EE](./java-in-process-agent.md)

## <a name="exploring-performance-metrics"></a><a name="view"></a>Exploration des mesures de performances
Sur le [portail Azure](https://portal.azure.com), accédez à la ressource Application Insights que vous avez configurée pour votre application. Le panneau de vue d’ensemble présente les données de performances de base :

Cliquez sur n’importe quel graphique pour afficher plus de détails et davantage de résultats et ce, pendant plus longtemps. Par exemple, cliquez sur la vignette Demandes et sélectionnez une plage de temps :

![Cliquez sur d'autres éléments pour afficher plus de données et sélectionnez une plage de temps](./media/web-monitor-performance/appinsights-48metrics.png)

Cliquez sur un graphique pour choisir les métriques à afficher, ou ajoutez un nouveau graphique et sélectionnez ses métriques :

![Cliquez sur un graphique pour sélectionner les métriques](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Décochez toutes les mesures** pour afficher toutes les options disponibles. Les métriques se répartissent en trois groupes ; lorsqu'un membre d'un groupe est sélectionné, seuls les autres membres de ce groupe s'affichent.

## <a name="what-does-it-all-mean-performance-tiles-and-reports"></a><a name="metrics"></a>Signification Vignettes de performances et rapports
Vous pouvez obtenir plusieurs indicateurs de performance. Commençons par celles qui s'affichent par défaut dans le volet de l'application.

### <a name="requests"></a>Demandes
Le nombre de demandes HTTP reçues pendant une période spécifiée. Comparez cette valeur avec les résultats des autres rapports pour savoir comment votre application se comporte lorsque la charge à laquelle elle est soumise varie.

Les demandes HTTP incluent toutes les demandes GET ou POST de pages, données et images.

Cliquez sur la vignette pour obtenir les décomptes associés aux URL spécifiques.

### <a name="average-response-time"></a>Temps de réponse moyen
Mesure le temps entre le moment où une demande Web est reçue par votre application et le moment où la réponse est renvoyée.

Les points indiquent une moyenne qui évolue. En présence de nombreuses demandes, certaines d'entre elles peuvent s'écarter de la moyenne sans indiquer de pic ou de creux dans le graphique.

Recherchez les pics inhabituels. En règle générale, il est normal que le temps de réponse augmente lorsque le nombre de demandes augmente. Si l'augmentation est disproportionnée, il se peut que votre application ait atteint une limite de ressources telles que l'UC ou la capacité d'un service qu'elle utilise.

Cliquez sur la vignette pour obtenir les temps des URL spécifiées.

![Capture d’écran du volet Intégrité de l’application, qui affiche des graphiques linéaires dans le temps relatifs aux moyennes mobiles des demandes et des temps de réponse.](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Demandes les plus lentes
![Capture d’écran d’une liste des demandes les plus lentes et de leurs temps de réponse.](./media/web-monitor-performance/appinsights-44slowest.png)

Indique les demandes dont les performances doivent probablement être améliorées.

### <a name="failed-requests"></a>Demandes ayant échoué
![Capture d’écran d’un graphique linéaire dans le temps relatif au nombre de demandes ayant échoué ; autrement dit, les demandes qui ont levé des exceptions non interceptées.](./media/web-monitor-performance/appinsights-46failed.png)

Décompte des demandes qui ont généré des exceptions non interceptées.

Cliquez sur la vignette pour afficher le détail des échecs spécifiques, puis sélectionnez une demande individuelle pour afficher ses informations détaillées. 

Seul un échantillon représentatif des échecs est prélevé pour chaque inspection individuelle.

### <a name="other-metrics"></a>Autres métriques
Pour savoir quelles autres métriques vous pouvez afficher, cliquez sur un graphique, puis désélectionnez toutes les métriques afin d'afficher l'ensemble des options disponibles. Cliquez sur (i) pour afficher la définition de chaque métrique.

![Désélectionnez toutes les métriques afin d'afficher l'ensemble des options disponibles](./media/web-monitor-performance/appinsights-62allchoices.png)

La sélection d’une métrique désactive celles qui ne peuvent pas s’afficher sur le même graphique.

## <a name="set-alerts"></a>Définir des alertes
Pour être averti par courrier électronique en cas de valeurs inhabituelles pour une métrique, ajoutez une alerte. Vous pouvez choisir d'envoyer le courrier électronique aux administrateurs de compte ou à des adresses de messagerie spécifiques.

![Capture d’écran de la boîte de dialogue Ajouter une règle d’alerte, ainsi que des captures d’écran reliées par des flèches qui montrent comment accéder à cette boîte de dialogue à partir de Metrics Explorer.](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Définissez la ressource avant les autres propriétés. Ne choisissez pas les ressources de test web si vous souhaitez définir des alertes pour les métriques de performances ou d'utilisation.

Veillez à noter les unités dans lesquelles vous êtes invité à entrer la valeur seuil.

*Je ne vois pas apparaître le bouton Ajouter une alerte.* - S’agit-il d’un compte de groupe auquel vous avez accès en lecture uniquement ? Consultez l’administrateur de ce compte.

## <a name="diagnosing-issues"></a><a name="diagnosis"></a>Problèmes de diagnostic
Voici quelques conseils pour identifier et diagnostiquer les problèmes de performances :

* Configurez des [tests web][availability] pour recevoir une alerte en cas de défaillance ou de réponse incorrecte/lente de votre site web. 
* Comparez le nombre de demandes avec les autres métriques afin de savoir si ces défaillances ou réponses lentes sont liées à la charge du site Web.
* [Insérez et recherchez des déclarations de trace][diagnostic] dans votre code pour identifier plus facilement les problèmes.
* Surveillez votre application web en cours de fonctionnement avec le [Flux de métriques temps réel][livestream].
* Capturez l’état de votre application .NET avec le [Débogueur de capture instantanée][snapshot].

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Rechercher et corriger les goulots d’étranglement avec une expérience d'analyse des performances

Vous pouvez utiliser l’expérience d’analyse des performances d’Application Insights pour examiner les opérations qui s’exécutent lentement dans votre application web. Vous pouvez sélectionner rapidement une opération lente spécifique et utiliser le [profileur](./profiler.md) pour analyser les causes profondes du ralentissement jusqu’au niveau du code. À l’aide de la nouvelle distribution de durée indiquée pour l’opération sélectionnée, vous pouvez en un clin d’œil évaluer la gravité du problème pour vos clients. Vous pouvez voir le nombre des interactions utilisateur qui sont concernées pour chaque opération lente. Dans l’exemple suivant, nous avons décidé d’observer de plus près l’environnement pour l’opération GET Customers/Details. Dans la distribution de la durée, nous constatons que trois pics sont visibles. Celui de gauche indique environ 400 ms et correspond à un environnement présentant une bonne réactivité. Le pic du milieu indique environ 1,2 s et représente un environnement médiocre. Enfin, à 3,6 s, une autre petite pointe représente l’environnement au 99e centile, susceptible de causer l’insatisfaction de nos clients. Cet environnement est dix fois plus lent que l’environnement avancé pour la même opération. 

![Pics de durée de trois GET clients/détails](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Pour obtenir une meilleure idée de l’environnement utilisateur de cette opération, nous pouvons sélectionner une plage temporelle plus longue. Nous pouvons ensuite également réduire l’examen à une fenêtre temporelle spécifique, au cours de laquelle l’opération s’est avérée lente. Dans l’exemple suivant, nous sommes passés de la plage par défaut de 24 heures à une plage de 7 jours, puis avons ciblé la fenêtre de temps de 9 h 47 à 12 h 47 entre le mardi 12 et le mercredi 13. La distribution de durée et le nombre d’exemples et de traces du profileur ont été mis à jour sur la droite.

![Trois pics de durée pour GET Customers/Details sur une plage de 7 jours avec une fenêtre de temps](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Pour mieux cibler les environnements trop lents, nous pouvons ensuite effectuer un zoom sur les durées qui tombent entre le 95e et 99e centile. Celles-ci représentent les 4 % d’interactions utilisateur qui se sont avérées lentes.

![Trois pics de durée pour GET Customers/Details sur une plage de 7 jours avec une fenêtre de temps](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Vous pouvez maintenant consulter des exemples représentatifs, en cliquant sur le bouton d’exemples ou sur les traces du profileur représentatives, en cliquant sur le bouton Traces du profileur. Dans cet exemple, quatre traces ont été collectées pour l’opération GET Customers/Details dans la fenêtre de temps et la durée de plage souhaitées.

Il peut arriver que le problème ne se situe pas dans le code, mais dans une dépendance appelée par celui-ci. Vous pouvez basculer vers l’onglet Dépendances dans la vue de triage des performances pour examiner ces dépendances ralenties. Par défaut, la vue des performances présente les moyennes de tendance, mais la valeur à examiner est le 95e centile (ou le 99e lorsque vous surveillez un service mature). Dans l’exemple suivant, nous avons examiné le ralentissement de la dépendance BLOB Azure, sur laquelle nous appelons PUT fabrikamaccount. Les bonnes expériences se regroupent aux alentours de 40 ms, tandis que les appels lents à la même dépendance sont trois fois plus lents, et se regroupent à 120 ms environ. Il suffit qu’un petit nombre de ces appels s’additionne pour que l’opération en question ralentisse visiblement. Vous pouvez explorer les exemples représentatifs et les traces du profileur de la même manière que dans l’onglet Opérations.

![Trois pics de durée pour GET Customers/Details sur une plage de 7 jours avec une fenêtre de temps](./media/web-monitor-performance/SlowDependencies95thTrend.png)

L’expérience d’analyse des performances montre des informations pertinentes en plus de l’exemple de jeu sur lequel vous avez décidé de vous concentrer. La meilleure façon d’examiner toutes les analyses disponibles consiste à passer sur une période de 30 jours, puis à sélectionner Global pour avoir une visibilité sur toutes les opérations du mois passé.

![Trois pics de durée pour GET Customers/Details sur une plage de 7 jours avec une fenêtre de temps](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next-steps"></a><a name="next"></a>Étapes suivantes
[Tests web][availability] : faites envoyer des requêtes web à votre application à intervalles réguliers et partout dans le monde.

[Capturer et rechercher des traces de diagnostic][diagnostic] : insérez des appels de trace et passez au crible les résultats afin d’identifier les problèmes.

[Suivi de l’utilisation][usage] : découvrez ce que font les utilisateurs avec votre application.

[Résolution des problèmes][qna] et Questions et réponses



<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[greenbrown]: ./asp-net.md
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ./live-stream.md
[snapshot]: ./snapshot-debugger.md

