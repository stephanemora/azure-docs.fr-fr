---
title: Gestion de la tarification et du volume de données Azure Application Insights | Microsoft Docs
description: Gérer les volumes de données de télémétrie et surveiller les coûts dans Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 06/21/2018
ms.author: mbullwin
ms.openlocfilehash: a401bc3ed68dc9273ac14cfd357b9c05111dd54c
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311103"
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gérer la tarification et le volume de données dans Application Insights

La tarification pour [Azure Application Insights][start] est basée sur le volume de données par application. Chaque ressource d’Application Insights est facturée comme un service distinct et s’ajoute à votre facture d’abonnement Azure.

Application Insights a deux plans tarifaires : De base et Entreprise. Le plan tarifaire De base est le plan par défaut. Il inclut toutes les fonctionnalités du plan Entreprise, sans frais supplémentaires. La facturation du plan De base repose essentiellement sur le volume de données ingéré. 

Le plan Entreprise donne lieu à une facturation par nœud, chaque nœud recevant une allocation de données quotidienne. Dans le plan tarifaire Entreprise, vous êtes facturé pour les données consommées au-delà de l’allocation incluse. Si vous utilisez Operations Management Suite, vous devez choisir le plan Entreprise. 

Si vous avez des questions sur les tarifs d’Application Insights, vous pouvez poster une question dans notre [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Plans tarifaires

Pour connaître les prix actuels dans votre devise et pour votre région, consultez [Tarification Application Insights][pricing].

> [!NOTE]
> En avril 2018, nous [introduisons](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) un nouveau modèle tarifaire pour la surveillance Azure. Ce modèle adopte un modèle de « paiement à l’utilisation » simple sur le portefeuille complet de services de surveillance. Découvrez le [nouveau modèle tarifaire](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), comment [évaluer l’impact du passage à ce modèle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) en fonction de vos habitudes d’utilisation et comment le [choisir](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Plan De base

Le plan De base est le plan tarifaire par défaut quand une ressource Application Insights est créée. Le plan De base convient pour tous les clients à l’exception de ceux qui ont un abonnement Operations Management Suite.

* Dans le plan De base, vous êtes facturé en fonction du volume de données, à savoir le nombre d’octets de données de télémétrie reçus par Application Insights. Le volume de données est mesuré comme la taille du package de données JSON non compressé reçu par Application Insights de la part de votre application. Pour [les données tabulaires importées dans Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), le volume de données est mesuré en tant que taille non compressée des fichiers envoyés à Application Insights.
* Les frais relatifs aux volumes de données de votre application figurent désormais sur un nouveau compteur de facturation nommé **Ingestion des données** depuis avril 2018. Ce compteur, partagé par plusieurs technologies de surveillance comme Applications Insights et Log Analytics, apparaît actuellement sous le nom de service **App Services** (et prochainement sous **Log Analytics**). 
* Les données des [Flux de métriques temps réel](app-insights-live-stream.md) ne sont pas comptabilisées dans la tarification.
* [L’exportation continue](app-insights-export-telemetry.md) et le [connecteur Azure Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) sont disponibles sans frais supplémentaires dans le plan De base du mois d’avril 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Plan Entreprise et droits d’abonnement à Operations Management Suite

Comme [annoncé récemment](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), les clients qui achètent Operations Management Suite E1 et E2 peuvent obtenir Application Insights Enterprise en tant que composant supplémentaire, sans frais supplémentaires. Plus précisément, chaque unité Operations Management Suite E1 et E2 inclut un droit à un nœud du plan Entreprise Application Insights. Chaque nœud Application Insights inclut l’intégration de 200 Mo de données maximum par jour (en plus de l’intégration des données Log Analytics), avec une période de rétention des données de 90 jours, sans coût supplémentaire. Le plan est décrit plus loin dans cet article. 

Ce plan ne s’appliquant qu’aux clients détenant un abonnement Operations Management Suite, ceux sans abonnement Operations Management Suite ne peuvent pas le sélectionner.

> [!NOTE]
> Pour pouvoir bénéficier de ce droit, vos ressources Application Insights doivent figurer dans le plan tarifaire Entreprise. Ce droit s’applique uniquement sous forme de nœuds. Les ressources Application Insights dans le plan De base n’en bénéficient pas. Ce droit n’est pas visible dans l’estimation des coûts figurant dans le volet **Utilisation et estimation des coûts**. De plus, si vous déplacez un abonnement vers le nouveau modèle tarifaire pour le monitoring Azure en avril 2018, le plan De base est le seul plan disponible. Nous vous déconseillons de déplacer un abonnement vers le nouveau modèle tarifaire pour le monitoring Azure si vous avez un abonnement Operations Management Suite.

Pour plus d’informations sur le plan Entreprise, consultez [Détails des tarifs Entreprise](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Tests web à plusieurs étapes

Les [tests web à plusieurs étapes](app-insights-monitor-web-app-availability.md#multi-step-web-tests) donnent lieu à des frais supplémentaires. Il s’agit de tests web qui exécutent une séquence d’actions.

Aucun frais supplémentaire n’est facturé pour les *tests Ping* sur une seule page. Les données de télémétrie des tests Ping et des tests à plusieurs étapes sont facturées comme les autres données de télémétrie de votre application.

## <a name="review-pricing-plans-and-estimate-costs"></a>Passer en revue les plans de tarification et estimer les coûts

Application Insights vous permet de comprendre facilement les plans tarifaires disponibles et d’estimer vos coûts en fonction des modèles d’utilisation récente. Pour commencer, dans le portail Azure, pour la ressource Application Insights, accédez au volet **Utilisation et estimation des coûts** :

![Choisir les tarifs](./media/app-insights-pricing/pricing-001.png)

R. Consultez votre volume de données pour le mois. Cela inclut toutes les données reçues et conservées (après tous les [échantillonnages](app-insights-sampling.md)) provenant de votre serveur, de vos applications clientes et des tests de disponibilité.  
B. Les [tests web multiétapes](app-insights-monitor-web-app-availability.md#multi-step-web-tests) font l’objet d’une facturation distincte. (Cela n’inclut pas les tests de disponibilité simples, qui sont compris dans les frais de volume de données.)  
C. Affichez les tendances des volumes de données pour le mois antérieur.  
D. Utilisez [l’échantillonnage](app-insights-sampling.md) d’ingestion des données.   
E. Définissez la limite quotidienne de volume de données.  

Les frais liés à Application Insights sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure s’affichent dans la section **Facturation** du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions). 

![Dans le menu de gauche, sélectionnez Facturation.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Débit de données
Le volume de données que vous envoyez est limité de trois façons :

* **Échantillonnage :** vous pouvez effectuer un échantillonnage pour réduire la quantité de données de télémétrie envoyées à partir de votre serveur et de vos applications clientes, avec une distorsion minimale des métriques. L’échantillonnage est le principal outil dont vous disposez pour ajuster la quantité de données que vous envoyez. Découvrez plus en détail les [fonctionnalités d’échantillonnage](app-insights-sampling.md). 
* **Limite quotidienne :** au moment où vous créez une ressource Application Insights dans le portail Azure, la limite quotidienne est définie à 100 Go/jour. Quand vous créez une ressource Application Insights dans Visual Studio, la valeur par défaut est faible (seulement 32,3 Mo/jour). La valeur par défaut de la limite quotidienne est définie pour faciliter les tests. L’idée est que l’utilisateur augmente la limite quotidienne avant de déployer l’application en production. 

    La limite maximale est de 1 000 Go/jour, à moins que vous en demandiez une plus élevée pour les besoins d’une application à fort trafic. 

    Soyez prudent quand vous définissez la limite quotidienne. Votre objectif doit être de *ne jamais atteindre la limite quotidienne*. Si vous atteignez la limite quotidienne, vous perdez des données pour le reste de la journée, ce qui vous empêche de surveiller votre application. Pour changer la limite quotidienne, utilisez l’option **Limite quotidienne de volume**. Vous pouvez accéder à cette option dans le volet **Utilisation et estimation des coûts** (comme décrit plus loin dans l’article).
    Nous avons supprimé la restriction sur certains types d’abonnements qui disposent d’un crédit qui n’a pas pu être utilisé pour Application Insights. Si l’abonnement impose une limite de dépense, la boîte de dialogue Limite quotidienne indique comment la supprimer et permet d’augmenter la limite quotidienne au-delà de 32,3 Mo/jour.
* **Limitation :** ce paramètre limite le débit de données à 32 000 événements par seconde, en moyenne sur 1 minute et par clé d'instrumentation.

*Que se passe-t-il si mon application dépasse le taux limite ?*

* Le volume de données que votre application envoie est évalué à chaque minute. S’il dépasse le taux par seconde moyen de plus d’une minute, le serveur refuse des demandes. Le SDK met les données en mémoire tampon, puis tente de les renvoyer. Il répartit un volume excessif sur plusieurs minutes. Si votre application envoie régulièrement des données au-dessus du taux limite, certaines données sont supprimées. (Les SDK ASP.NET, Java et JavaScript essaient de renvoyer les données de cette manière ; les autres SDK suppriment parfois simplement les données limitées.) En cas de limitation, vous en êtes informé par un avertissement.

*Comment déterminer la quantité de données envoyées par mon application ?*

Vous pouvez utiliser une des options suivantes pour voir la quantité de données qu’envoie votre application :

* Accédez au volet **Utilisation et estimation des coûts** pour voir le graphique du volume quotidien de données. 
* Dans Metrics Explorer, ajoutez un nouveau graphique. Pour la métrique du graphique, sélectionnez **Volume de point de données**. Activez **Regroupement**, puis regroupez par **Type de données**.

## <a name="reduce-your-data-rate"></a>Réduire le débit de données
Voici quelques opérations possibles pour réduire le volume de données :

* Utilisez l’ [échantillonnage](app-insights-sampling.md). Cette technologie permet de réduire votre débit de données sans entraîner de distorsion de vos métriques. Vous gardez la possibilité de naviguer entre les éléments associés dans la recherche. Dans les applications serveurs, l’échantillonnage s’applique automatiquement.
* [Limitez le nombre d’appels Ajax qui peuvent être signalés](app-insights-javascript.md#detailed-configuration) dans chaque affichage de page, ou désactivez les rapports Ajax.
* [Modifiez ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) pour désactiver les modules de collecte dont vous n’avez pas besoin. Par exemple, vous pouvez décider que les compteurs de performances ou les données de dépendance ne sont pas essentiels.
* Répartissez vos données de télémétrie entre les clés d’instrumentation. 
* Procédez à la pré-agrégation des métriques. Si vous placez des appels de TrackMetric dans votre application, vous pouvez réduire le trafic en utilisant la surcharge qui accepte votre calcul de la moyenne et de l’écart type d’un lot de mesures. Une autre possibilité consiste à utiliser un [package de pré-agrégation](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Gérer le volume de données maximal quotidien

Vous pouvez utiliser la limite quotidienne de volume pour limiter les données collectées. Toutefois, si la limite est atteinte, toutes les données de télémétrie envoyées à partir de votre application sont perdues pour le reste de la journée. Il est *déconseillé* que votre application atteigne la limite quotidienne. Vous ne pouvez pas suivre l’intégrité et les performances de votre application une fois qu’elle a atteint la limite quotidienne.

Au lieu d’utiliser la limite quotidienne de volume, utilisez [l’échantillonnage](app-insights-sampling.md) pour régler le volume de données sur le niveau souhaité. Ensuite, n’utilisez la limite quotidienne qu’en « dernier recours », au cas où votre application commencerait soudainement à envoyer des volumes de données de télémétrie beaucoup plus élevés.

Pour changer la limite quotidienne, accédez à la section **Configurer** de votre ressource Application Insights dans le volet **Utilisation et estimation des coûts**, puis sélectionnez **Limite quotidienne**.

![Ajuster la limite du volume quotidien des données de télémétrie](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>échantillonnage
[L’échantillonnage](app-insights-sampling.md) est une méthode vous permettant de réduire la fréquence d’envoi des données de télémétrie à votre application, tout en conservant la capacité à trouver des événements connexes pendant les recherches de diagnostic. Vous conservez également le décompte des événements corrects.

L’échantillonnage est un moyen efficace de réduire les coûts et de respecter votre quota mensuel. L’algorithme d’échantillonnage conserve les éléments associés à la télémétrie ; ainsi, quand vous utilisez la recherche par exemple, vous pouvez trouver la demande liée à une exception spécifique. L’algorithme conserve également le décompte correct. Cela vous permet de voir les valeurs correctes des taux de demandes, des taux d’exception et des autres compteurs dans Metric Explorer.

Il existe plusieurs formes d’échantillonnage.

* [L’échantillonnage adaptatif](app-insights-sampling.md) est la méthode par défaut pour le SDK ASP.NET. L’échantillonnage adaptatif s’ajuste automatiquement au volume de données de télémétrie envoyées par votre application. Il fonctionne automatiquement dans le SDK de votre application web, afin de réduire le trafic de télémétrie sur le réseau. 
* *échantillonnage d’ingestion* est une méthode alternative qui fonctionne au niveau où les données de télémétrie issues de votre application entrent dans le service Application Insights. L’échantillonnage d’ingestion n’affecte pas le volume de télémétrie envoyé depuis votre application, mais il réduit le volume conservé par le service. Vous pouvez utiliser l’échantillonnage d’ingestion pour réduire le quota utilisé par les données de télémétrie provenant des navigateurs et d’autres SDK.

Pour définir l’échantillonnage d’ingestion, accédez au volet **Tarification** :

![Dans le volet Quotas et tarification, sélectionnez la vignette Exemples, puis sélectionnez une fraction d’échantillonnage.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> Le volet **Échantillonnage des données** contrôle uniquement la valeur d’échantillonnage de l’ingestion. Il ne reflète pas le taux d’échantillonnage qui est appliqué par le SDK Application Insights dans votre application. Si les données de télémétrie entrantes ont déjà été échantillonnées dans le SDK, l’échantillonnage d’ingestion n’est pas appliqué.
>

Pour découvrir le taux d’échantillonnage réel, indépendamment de l’endroit où il a été appliqué, utilisez une [requête Analytics](app-insights-analytics.md). La requête ressemble à ceci :

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Pour chaque enregistrement conservé, `itemCount` indique le nombre d’enregistrements d’origine qu’il représente. Ce nombre est égal à 1 + le nombre d’enregistrements précédents ignorés. 

## <a name="automation"></a>Automatisation

Vous pouvez utiliser le service de gestion des ressources Azure pour écrire un script de définition de plan tarifaire. [Découvrez comment](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Synthèse des limites

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Échantillonnage](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/