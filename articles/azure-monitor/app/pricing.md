---
title: Gérer l’utilisation et les coûts pour Azure Application Insights | Microsoft Docs
description: Gérer les volumes de données de télémétrie et surveiller les coûts dans Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 09/17/2019
ms.author: dalek
ms.openlocfilehash: 62f2ea36468e30b20ef08bde21bfde961faae8f9
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067011"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Gérer l’utilisation et les coûts pour Application Insights

> [!NOTE]
> Cet article explique comment analyser l’utilisation des données dans Application Insights.  Pour plus d’informations, consultez les articles suivants.
> - L’article [Monitoring usage and estimated costs](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) (Surveillance de l’utilisation et estimation des coûts) explique comment visualiser l’utilisation et les coûts estimés avec plusieurs fonctionnalités de surveillance Azure en fonction des différents modèles de tarification. Il explique également comment modifier votre modèle de tarification.

Si vous avez des questions sur les tarifs d’Application Insights, vous pouvez poster une question dans notre [forum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Modèle de tarification

Les tarifs pour [Azure Application Insights][start] sont basés sur le volume de données ingérées. Chaque ressource d’Application Insights est facturée comme un service distinct et s’ajoute à votre facture d’abonnement Azure.

### <a name="data-volume-details"></a>Détails du volume de données

* à savoir le nombre d’octets de données de télémétrie reçus par Application Insights. Le volume de données est mesuré comme la taille du package de données JSON non compressé reçu par Application Insights de la part de votre application. Pour [les données tabulaires importées dans Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), le volume de données est mesuré en tant que taille non compressée des fichiers envoyés à Application Insights.
* Les frais relatifs aux volumes de données de votre application figurent désormais sur un nouveau compteur de facturation nommé **Ingestion des données** depuis avril 2018. Ce nouveau compteur est partagé par plusieurs technologies de supervision comme Applications Insights et Log Analytics, et apparaît actuellement sous le nom de service **Log Analytics**. 
* Les données des [Flux de métriques temps réel](../../azure-monitor/app/live-stream.md) ne sont pas comptabilisées dans la tarification.

> [!NOTE]
> Tous les prix indiqués dans les captures d’écran de cet article sont fournis uniquement à titre d’exemple. Pour connaître les prix actuels dans votre devise et pour votre région, consultez la page [Tarification Application Insights][pricing].

### <a name="multi-step-web-tests"></a>Tests web à plusieurs étapes

Les [tests web à plusieurs étapes](../../azure-monitor/app/availability-multistep.md) donnent lieu à des frais supplémentaires. Il s’agit de tests web qui exécutent une séquence d’actions.

Aucun frais supplémentaire n’est facturé pour les *tests Ping* sur une seule page. Les données de télémétrie des tests Ping et des tests à plusieurs étapes sont facturées comme les autres données de télémétrie de votre application.

## <a name="review-usage-and-estimate-costs"></a>Réviser l’utilisation et estimer les coûts

Application Insights vous permet de comprendre facilement à combien vont s’élever vos coûts en fonction des modèles d’utilisation récente. Pour commencer, dans le portail Azure, pour la ressource Application Insights, accédez à la page **Utilisation et estimation des coûts** :

![Choisir les tarifs](./media/pricing/pricing-001.png)

R. Consultez votre volume de données pour le mois. Cela inclut toutes les données reçues et conservées (après tous les [échantillonnages](../../azure-monitor/app/sampling.md)) provenant de votre serveur, de vos applications clientes et des tests de disponibilité.  
B. Les [tests web multiétapes](../../azure-monitor/app/availability-multistep.md) font l’objet d’une facturation distincte. (Cela n’inclut pas les tests de disponibilité simples, qui sont compris dans les frais de volume de données.)  
C. Affichez les tendances des volumes de données pour le mois antérieur.  
D. Utilisez [l’échantillonnage](../../azure-monitor/app/sampling.md) d’ingestion des données.   
E. Définissez la limite quotidienne de volume de données.  

Pour étudier votre utilisation d’Application Insights de façon plus approfondie, ouvrez la page **Métriques**, ajoutez la métrique nommée « Volume de point de données », puis sélectionnez l’option *Appliquer la division* pour répartir les données selon le « type de l’élément de télémétrie ». 

Les frais liés à Application Insights sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure s’affichent dans la section **Facturation** du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions). 

![Dans le menu de gauche, sélectionnez Facturation.](./media/pricing/02-billing.png)

## <a name="managing-your-data-volume"></a>Gestion de votre volume de données 

Pour déterminer la quantité de données envoyées par votre application, vous pouvez effectuer les opérations suivantes :

* Accédez au volet **Utilisation et estimation des coûts** pour voir le graphique du volume quotidien de données. 
* Dans Metrics Explorer, ajoutez un nouveau graphique. Pour la métrique du graphique, sélectionnez **Volume de point de données**. Activez **Regroupement**, puis regroupez par **Type de données**.
* Utilisez le type de données `systemEvents`. Par exemple, pour voir le volume de données ingéré au cours du dernier jour, la requête serait la suivante :

```kusto
systemEvents 
| where timestamp >= ago(1d)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Cette requête peut être utilisée dans une [alerte de journal Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) pour configurer des alertes sur des volumes de données. 

Le volume de données que vous envoyez peut être géré de trois façons :

* **Échantillonnage** : vous pouvez effectuer un échantillonnage pour réduire la quantité de données de télémétrie envoyées à partir de votre serveur et de vos applications clientes, avec une distorsion minimale des métriques. L’échantillonnage est le principal outil dont vous disposez pour ajuster la quantité de données que vous envoyez. Découvrez plus en détail les [fonctionnalités d’échantillonnage](../../azure-monitor/app/sampling.md).
 
* **Limite quotidienne** : au moment où vous créez une ressource Application Insights dans le Portail Azure, la limite quotidienne est définie à 100 Go/jour. Quand vous créez une ressource Application Insights dans Visual Studio, la valeur par défaut est faible (seulement 32,3 Mo/jour). La valeur par défaut de la limite quotidienne est définie pour faciliter les tests. L’idée est que l’utilisateur augmente la limite quotidienne avant de déployer l’application en production. 

    La limite maximale est de 1 000 Go/jour, à moins que vous en demandiez une plus élevée pour les besoins d’une application à fort trafic. 
    
    Les e-mails d’avertissement relatifs à la limite quotidienne sont envoyés aux comptes qui sont membres de ces rôles pour votre ressource Application Insights : « ServiceAdmin », « AccountAdmin », « CoAdmin », « Owner ».

    Soyez prudent quand vous définissez la limite quotidienne. Votre objectif doit être de *ne jamais atteindre la limite quotidienne*. Si vous atteignez la limite quotidienne, vous perdez des données pour le reste de la journée, ce qui vous empêche de surveiller votre application. Pour changer la limite quotidienne, utilisez l’option **Limite quotidienne de volume**. Vous pouvez accéder à cette option dans le volet **Utilisation et estimation des coûts** (comme décrit plus loin dans l’article).
    
    Nous avons supprimé la restriction sur certains types d’abonnements qui disposent d’un crédit qui n’a pas pu être utilisé pour Application Insights. Si l’abonnement impose une limite de dépense, la boîte de dialogue Limite quotidienne indique comment la supprimer et permet d’augmenter la limite quotidienne au-delà de 32,3 Mo/jour.
    
* **Limitation** : ce paramètre limite le débit de données à 32 000 événements par seconde, en moyenne sur 1 minute et par clé d’instrumentation. Le volume de données que votre application envoie est évalué à chaque minute. S’il dépasse le taux par seconde moyen de plus d’une minute, le serveur refuse des demandes. Le SDK met les données en mémoire tampon, puis tente de les renvoyer. Il répartit un volume excessif sur plusieurs minutes. Si votre application envoie régulièrement des données au-dessus du taux limite, certaines données sont supprimées. (Les SDK ASP.NET, Java et JavaScript essaient de renvoyer les données de cette manière ; les autres SDK suppriment parfois simplement les données limitées.) En cas de limitation, vous en êtes informé par un avertissement.

## <a name="reduce-your-data-volume"></a>Réduire le volume de données

Voici quelques opérations possibles pour réduire le volume de données :

* Utilisez l’ [échantillonnage](../../azure-monitor/app/sampling.md). Cette technologie permet de réduire votre débit de données sans entraîner de distorsion de vos métriques. Vous gardez la possibilité de naviguer entre les éléments associés dans la recherche. Dans les applications serveurs, l’échantillonnage s’applique automatiquement.
* [Limitez le nombre d’appels Ajax qui peuvent être signalés](../../azure-monitor/app/javascript.md#configuration) dans chaque affichage de page, ou désactivez les rapports Ajax.
* [Modifiez ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) pour désactiver les modules de collecte dont vous n’avez pas besoin. Par exemple, vous pouvez décider que les compteurs de performances ou les données de dépendance ne sont pas essentiels.
* Répartissez vos données de télémétrie entre les clés d’instrumentation. 
* Procédez à la pré-agrégation des métriques. Si vous placez des appels de TrackMetric dans votre application, vous pouvez réduire le trafic en utilisant la surcharge qui accepte votre calcul de la moyenne et de l’écart type d’un lot de mesures. Une autre possibilité consiste à utiliser un [package de pré-agrégation](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Gérer le volume de données maximal quotidien

Vous pouvez utiliser la limite quotidienne de volume pour limiter les données collectées. Toutefois, si la limite est atteinte, toutes les données de télémétrie envoyées à partir de votre application sont perdues pour le reste de la journée. Il est *déconseillé* que votre application atteigne la limite quotidienne. Vous ne pouvez pas suivre l’intégrité et les performances de votre application une fois qu’elle a atteint la limite quotidienne.

Au lieu d’utiliser la limite quotidienne de volume, utilisez [l’échantillonnage](../../azure-monitor/app/sampling.md) pour régler le volume de données sur le niveau souhaité. Ensuite, n’utilisez la limite quotidienne qu’en « dernier recours », au cas où votre application commencerait soudainement à envoyer des volumes de données de télémétrie beaucoup plus élevés.

Pour changer la limite quotidienne, accédez à la section **Configurer** de votre ressource Application Insights, dans la page **Utilisation et estimation des coûts**, puis sélectionnez **Limite quotidienne**.

![Ajuster la limite du volume quotidien des données de télémétrie](./media/pricing/pricing-003.png)

Pour [modifier la limite quotidienne via Azure Resource Manager](../../azure-monitor/app/powershell.md), la propriété à modifier est `dailyQuota`.  Avec Azure Resource Manager, vous pouvez également définir `dailyQuotaResetTime` et `warningThreshold` pour le plafond quotidien. 

## <a name="sampling"></a>échantillonnage
[L’échantillonnage](../../azure-monitor/app/sampling.md) est une méthode vous permettant de réduire la fréquence d’envoi des données de télémétrie à votre application, tout en conservant la capacité à trouver des événements connexes pendant les recherches de diagnostic. Vous conservez également le décompte des événements corrects.

L’échantillonnage est un moyen efficace de réduire les coûts et de respecter votre quota mensuel. L’algorithme d’échantillonnage conserve les éléments associés à la télémétrie ; ainsi, quand vous utilisez la recherche par exemple, vous pouvez trouver la demande liée à une exception spécifique. L’algorithme conserve également le décompte correct. Cela vous permet de voir les valeurs correctes des taux de demandes, des taux d’exception et des autres compteurs dans Metric Explorer.

Il existe plusieurs formes d’échantillonnage.

* [L’échantillonnage adaptatif](../../azure-monitor/app/sampling.md) est la méthode par défaut pour le SDK ASP.NET. L’échantillonnage adaptatif s’ajuste automatiquement au volume de données de télémétrie envoyées par votre application. Il fonctionne automatiquement dans le SDK de votre application web, afin de réduire le trafic de télémétrie sur le réseau. 
* *échantillonnage d’ingestion* est une méthode alternative qui fonctionne au niveau où les données de télémétrie issues de votre application entrent dans le service Application Insights. L’échantillonnage d’ingestion n’affecte pas le volume de télémétrie envoyé depuis votre application, mais il réduit le volume conservé par le service. Vous pouvez utiliser l’échantillonnage d’ingestion pour réduire le quota utilisé par les données de télémétrie provenant des navigateurs et d’autres SDK.

Pour définir l’échantillonnage d’ingestion, accédez au volet **Tarification** :

![Dans le volet Quotas et tarification, sélectionnez la vignette Exemples, puis sélectionnez une fraction d’échantillonnage.](./media/pricing/pricing-004.png)

> [!WARNING]
> Le volet **Échantillonnage des données** contrôle uniquement la valeur d’échantillonnage de l’ingestion. Il ne reflète pas le taux d’échantillonnage qui est appliqué par le SDK Application Insights dans votre application. Si les données de télémétrie entrantes ont déjà été échantillonnées dans le SDK, l’échantillonnage d’ingestion n’est pas appliqué.
>

Pour découvrir le taux d’échantillonnage réel, indépendamment de l’endroit où il a été appliqué, utilisez une [requête Analytics](analytics.md). La requête ressemble à ceci :

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Pour chaque enregistrement conservé, `itemCount` indique le nombre d’enregistrements d’origine qu’il représente. Ce nombre est égal à 1 + le nombre d’enregistrements précédents ignorés. 

## <a name="change-the-data-retention-period"></a>Changer la période de rétention des données

> [!NOTE]
> Nous avons temporairement supprimé cette fonctionnalité pendant que nous répondons à un problème potentiel.  Nous la rétablirons d’ici la première semaine d'octobre 2019.

La rétention par défaut pour les ressources Application Insights est de 90 jours. Différentes périodes de rétention peuvent être sélectionnées pour chaque ressource Application Insights. L’ensemble complet de périodes de conservation disponibles est 30, 60, 90, 120, 180, 270, 365, 550 et 730 jours. 

Pour changer le délai de conservation, dans votre ressource Application Insights, accédez à la page **Utilisation et coûts estimés**, puis sélectionnez l’option **Conservation des données** :

![Ajuster la limite du volume quotidien des données de télémétrie](./media/pricing/pricing-005.png)

Quand la facturation est activée pour une rétention plus longue, les données conservées pendant plus de 90 jours sont facturées selon le même tarif que celles actuellement facturées pour la conservation des données Azure Log Analytics. Apprenez-en davantage dans la [page des tarifs Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Tenez-vous informé de la progression de la rétention variable en [votant pour cette suggestion](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031). 

## <a name="limits-summary"></a>Synthèse des limites

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Désactiver les e-mails de limite quotidienne

Pour désactiver les e-mails de limite de volume quotidienne, accédez à la section **Configurer** de votre ressource Application Insights dans le volet **Utilisation et estimation des coûts**, puis sélectionnez **Limite quotidienne**. Il existe des paramètres pour envoyer un e-mail quand la limite est atteinte, ainsi que quand un niveau d’avertissement réglable a été atteint. Si vous souhaitez désactiver tous les e-mails liés à la limite de volume quotidienne, décochez les deux cases.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Niveau tarifaire Entreprise existant (par nœud)

Pour les utilisateurs initiaux d’Azure Application Insights, il existe toujours deux niveaux tarifaires possibles : De base et Entreprise. Le niveau tarifaire De base est identique à celui décrit ci-dessus ; il s’agit du niveau par défaut. Il inclut toutes les fonctionnalités du niveau Entreprise, sans frais supplémentaires. La facturation du niveau De base repose essentiellement sur le volume de données ingérées. 

> [!NOTE]
> Ces niveaux tarifaires existants ont été renommés. Le niveau tarifaire Entreprise et le niveau tarifaire De base sont désormais nommés **Par nœud** et **Par Go**, respectivement. Ces deux niveaux sont désignés sous leur nouveau nom ci-après et dans le portail Azure.  

Le niveau Par nœud (anciennement Entreprise) donne lieu à une facturation pour chaque nœud, chaque nœud recevant une allocation de données quotidienne. Dans le niveau tarifaire Par nœud, vous êtes facturé pour les données ingérées au-delà de l’allocation incluse. Si vous utilisez Operations Management Suite, vous devez choisir le niveau Par nœud. 

Pour connaître les prix actuels dans votre devise et pour votre région, consultez la page [Tarification Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> En avril 2018, nous [introduisons](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) un nouveau modèle tarifaire pour la surveillance Azure. Ce modèle adopte un modèle de « paiement à l’utilisation » simple sur le portefeuille complet de services de surveillance. En savoir plus sur le [nouveau modèle tarifaire](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), sur [l’évaluation de l’impact du passage à ce modèle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) en fonction de vos modèles d’utilisation et sur [l’adoption du nouveau modèle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Niveau tarifaire Par nœud et droits d’abonnement à Operations Management Suite

Comme [annoncé récemment](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), les clients qui achètent Operations Management Suite E1 et E2 peuvent obtenir Application Insights Par nœud comme composant supplémentaire, sans frais supplémentaires. Plus précisément, chaque unité Operations Management Suite E1 et E2 donne droit à un nœud Application Insights du niveau Par nœud. Chaque nœud Application Insights inclut l’intégration de 200 Mo de données maximum par jour (en plus de l’intégration des données Log Analytics), avec une période de rétention des données de 90 jours, sans coût supplémentaire. Le niveau est décrit plus loin dans cet article. 

Ce niveau ne s’appliquant qu’aux clients détenant un abonnement Operations Management Suite, ceux sans abonnement Operations Management Suite ne peuvent pas le sélectionner.

> [!NOTE]
> Pour pouvoir bénéficier de ce droit, vos ressources Application Insights doivent faire partie du niveau tarifaire Par nœud. Ce droit s’applique uniquement sous forme de nœuds. Les ressources Application Insights fournies dans le plan Par Go n’en bénéficient pas. Ce droit n’est pas visible dans l’estimation des coûts figurant dans le volet **Utilisation et estimation des coûts**. De plus, si vous déplacez un abonnement vers le nouveau modèle tarifaire de surveillance Azure d’avril 2018, le plan Par Go est le seul niveau disponible. Nous vous déconseillons de déplacer un abonnement vers le nouveau modèle tarifaire pour le monitoring Azure si vous avez un abonnement Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Fonctionnement du niveau Par nœud

* Vous payez pour chaque nœud qui envoie des données de télémétrie relatives à une application dans le niveau Par nœud.
  * Un *nœud* correspond à une machine serveur virtuelle ou physique ou à une instance de rôle PaaS (Platform-as-a-Service) qui héberge votre application.
  * Les ordinateurs de développement, les navigateurs clients et les appareils mobiles ne sont pas considérés comme des nœuds.
  * Si votre application comporte plusieurs composants qui envoient des données de télémétrie, comme un service web et un Worker principal, ces composants sont comptabilisés séparément.
  * Les données des [Flux de métriques temps réel](../../azure-monitor/app/live-stream.md) ne sont pas comptabilisées dans la tarification. Dans un abonnement, vos frais sont calculés par nœud, et non par application. Si vous disposez de cinq nœuds envoyant des données de télémétrie pour 12 applications, les frais sont calculés pour cinq nœuds.
* Bien que les frais indiqués soient par mois, vous êtes facturé uniquement pour toutes les heures dans lesquelles un nœud envoie des données de télémétrie à partir d’une application. Le tarif horaire est le prix mensuel indiqué divisé par 744 (nombre d’heures dans un mois de 31 jours).
* Une allocation de volume de données de 200 Mo par jour est accordée pour chaque nœud détecté (avec une granularité par heure). Le volume de données alloué inutilisé n’est pas reporté de jour en jour.
  * Si vous choisissez le niveau tarifaire Par nœud, chaque abonnement reçoit un volume quotidien de données en fonction du nombre de nœuds qui envoient des données de télémétrie aux ressources Application Insights dans cet abonnement. Par conséquent, si vous disposez de cinq nœuds qui envoient des données toute la journée, une allocation groupée de 1 Go est appliquée à toutes les ressources Application Insights de cet abonnement. Le fait que certains nœuds envoient plus de données que d’autres a peu d’importance, car les données incluses sont partagées entre tous les nœuds. Si, un jour donné, les ressources Application Insights reçoivent plus de données que le volume quotidien alloué pour cet abonnement, les frais de données de dépassement par Go s’appliquent. 
  * Le volume de données quotidien alloué est calculé comme suit : nombre d’heures quotidiennes (UTC) pendant lesquelles chaque nœud envoie des données de télémétrie, divisé par 24 multiplié par 200 Mo. Par conséquent, si quatre nœuds envoient des données de télémétrie pendant 15 heures sur les 24 heures de la journée, les données incluses pour le jour en question sont calculées comme suit : ((4 &#215; 15) / 24) &#215; 200 Mo = 500 Mo. Au prix de 2,30 USD par Go pour le dépassement de données, les frais s'élèvent à 1,15 USD si les nœuds envoient 1 Go de données dans cette journée.
  * Le volume de données quotidien alloué du niveau Par nœud n’est pas partagé entre les applications pour lesquelles vous avez choisi le niveau Par Go. Le volume alloué inutilisé n’est pas reporté de jour en jour. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Exemples de détermination du nombre de nœuds distincts

| Scénario                               | Nombre total de nœuds quotidien |
|:---------------------------------------|:----------------:|
| 1 application utilisant 3 instances Azure App Service et 1 serveur virtuel | 4 |
| 3 applications s’exécutant sur 2 machines virtuelles ; ressources Application Insights pour ces applications figurant dans le même abonnement et dans le niveau Par nœud | 2 | 
| 4 applications dont les ressources Applications Insights figurent dans le même abonnement ; chaque application exécutant 2 instances pendant 16 heures creuses, ainsi que 4 instances pendant 8 heures de pointe | 13.33 | 
| Services cloud avec 1 rôle de travail et 1 rôle web, chacune exécutant 2 instances | 4 | 
| Cluster Azure Service Fabric de 5 nœuds exécutant 50 microservices ; chaque microservice exécutant 3 instances | 5\.|

* Le comptage de nœuds précis varie selon le Kit de développement logiciel (SDK) Application Insights que votre application utilise. 
  * Dans les versions 2.2 et ultérieures de ce Kit de développement logiciel, le [Kit de développement logiciel (SDK) principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) et le [Kit de développement logiciel (SDK) web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) Application Insights signalent tous deux chaque hôte d’application en tant que nœud, par exemple le nom d’ordinateur pour le serveur physique et les hôtes de machine virtuelle ou le nom d’instance dans le cas de services cloud.  La seule exception concerne une application qui utilise uniquement [.NET Core](https://dotnet.github.io/) et le Kit de développement logiciel (SDK) principal Application Insights, auquel cas un seul nœud est signalé pour tous les hôtes, car le nom d’hôte n’est pas disponible. 
  * Pour les versions antérieures du Kit de développement logiciel (SDK), le [Kit de développement logiciel (SDK) web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se comporte comme les nouvelles versions du Kit de développement logiciel (SDK), mais le [Kit de développement logiciel (SDK) principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) ne signale qu’un seul nœud, quel que soit le nombre d’hôtes d’application. 
  * Si votre application utilise le Kit de développement logiciel (SDK) pour définir l’instance de rôle (**roleInstance**) sur une valeur personnalisée, cette même valeur est utilisée par défaut pour déterminer le nombre de nœuds. 
  * Si vous utilisez une nouvelle version du Kit de développement logiciel (SDK) avec une application qui s’exécute à partir d’ordinateurs clients ou d’appareils mobiles, le nombre de nœuds renvoyé peut se révéler très important (en raison du grand nombre d’ordinateurs clients ou d’appareils mobiles). 

## <a name="automation"></a>Automatisation

Vous pouvez utiliser le service Azure Resource Management pour écrire un script de définition du niveau tarifaire. [Découvrez comment](powershell.md#price).


## <a name="next-steps"></a>Étapes suivantes

* [Échantillonnage](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/