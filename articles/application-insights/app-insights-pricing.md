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
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Gérer la tarification et le volume de données dans Application Insights

La tarification pour [Azure Application Insights][start] est basée sur le volume de données par application. Chaque ressource d’Application Insights est facturée comme un service distinct et s’ajoute à votre facture d’abonnement à Azure.

Il existe deux plans de tarification. Le plan par défaut dit De base intègre les mêmes fonctionnalités que le plan Entreprise sans frais supplémentaires. Il est facturé principalement d’après le volume de données consommé. Si vous utilisez Operations Management Suite, vous devez choisir le plan Entreprise. Ce dernier est facturé d’après le nombre de nœuds et les dotations de données quotidiennes. Vous êtes ensuite facturé pour toutes les données consommées en supplément de la dotation choisie.

Si vous avez des questions sur la tarification d’Application Insights, publiez une question dans notre [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="the-price-plans"></a>Les plans tarifaires

Pour connaître les prix actuels dans votre devise et pour votre région, consultez la [page de tarification d’Application Insights][pricing].

### <a name="basic-plan"></a>Plan De base

Le plan De base est le plan par défaut lorsqu’une nouvelle ressource Application Insights est créée. Il est idéal pour tous les clients, à l’exception de ceux avec un abonnement Operations Management Suite.

* Dans le plan De base, vous êtes facturé en fonction du volume de données : nombre d’octets de données de télémétrie reçus par Application Insights. Le volume de données est mesuré comme la taille du package de données JSON non compressé reçu par Application Insights de la part de votre application.
Pour [les données tabulaires importées dans Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), le volume de données est mesuré en tant que taille non compressée des fichiers envoyés à Application Insights.
* Les données des [Flux de métriques temps réel](app-insights-live-stream.md) ne sont pas comptabilisées dans la tarification.
* [L’exportation continue](app-insights-export-telemetry.md) et le [connecteur Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) sont disponibles sans frais supplémentaires dans le plan De base du mois d’avril 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Plan Entreprise et droits d’abonnement à Operations Management Suite

Comme [annoncé récemment](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), les clients qui achètent Microsoft Operations Management Suite E1 et E2 peuvent obtenir Application Insights Enterprise en tant que composant supplémentaire, sans frais supplémentaires. Plus précisément, chaque unité Operations Management Suite E1 et E2 inclut un droit à 1 nœud du plan Entreprise d’Application Insights. Comme détaillé ci-dessous, chaque nœud Application Insights inclut jusqu’à 200 Mo de données consommées par jour (en plus de l’ingestion des données Log Analytics), sans frais supplémentaires et avec une période de conservation des données de 90 jours. Cela s’applique uniquement aux clients détenant un abonnement Operations Management Suite. Aussi, ceux sans abonnement ne peuvent pas sélectionner ce plan.

> [!NOTE]
> Pour pouvoir bénéficier de ce droit, vos ressources Application Insights doivent figurer dans le plan de tarification Entreprise. Ce droit s’applique uniquement sous forme de nœuds, et les ressources Application Insights du plan De base ne pourront donc pas en bénéficier. Notez que ce droit n’est pas visible dans l’estimation des coûts figurant sur la page des *coûts d’utilisation et estimés*. De même, si vous déplacez un abonnement dans le nouveau modèle de tarification de surveillance Azure d’avril 2018, le plan De base sera le seul disponible. Aussi, nous ne conseillons pas de procéder ainsi si vous possédez un abonnement Operations Management Suite.

Pour plus d’informations sur le plan Entreprise, consultez la [page sur la tarification Enterprise](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Tests web à plusieurs étapes

Des frais supplémentaires sont facturés pour les [tests web à plusieurs étapes](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Cela vaut pour les tests web qui exécutent une séquence d’actions.

Aucun frais supplémentaire n'est facturé pour les tests Ping sur une seule page. Les données de télémétrie des tests Ping et des tests à plusieurs étapes sont facturées, ainsi que d’autres données de télémétrie de votre application.

## <a name="review-pricing-plans-and-estimate-costs"></a>Passer en revue les plans de tarification et estimer les coûts

Application Insights permet de comprendre facilement les plans tarifaires disponibles et d’estimer les coûts en fonction des modèles d’utilisation récente. Commencez par ouvrir la page des **coûts d’utilisation et estimés** dans la ressource Application Insights du Portail Azure :

![Choisissez Tarification.](./media/app-insights-pricing/pricing-001.png)

**a.** Consultez votre volume de données pour le mois. Cela inclut toutes les données reçues et conservées (après tous les [échantillonnages](app-insights-sampling.md) provenant de votre serveur, de vos applications clientes et des tests de disponibilité).

**b.** Les [tests web multiétapes](app-insights-monitor-web-app-availability.md#multi-step-web-tests) font l’objet d’une facturation distincte. (Cela n’inclut pas les tests de disponibilité simples, qui sont compris dans les frais de volume de données.)

**c.** Affichez les tendances des volumes de données pour le mois dernier.

**d.** Utilisez [l’échantillonnage](app-insights-sampling.md) d’ingestion des données. 

**e.** Configurez la limite quotidienne de volume de données.

Les frais liés à Application Insights sont ajoutés à votre facture Azure. Les informations relatives à votre facture Azure s’affichent dans la section Facturation du portail Azure ou sur le [portail de facturation Azure](https://account.windowsazure.com/Subscriptions). 

![Dans le menu latéral, choisissez Facturation.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Débit de données
Le volume d’envoi de données est limité de trois façons :

* **Échantillonnage :** ce mécanisme permet de réduire la quantité de données de télémétrie envoyées à partir de votre serveur et de vos applications clientes, avec une distorsion minimale des métriques. C’est le principal outil dont vous disposez pour ajuster la quantité de données. Découvrez plus en détail les [fonctionnalités d’échantillonnage](app-insights-sampling.md). 
* **Limite quotidienne :** au moment de créer une ressource Application Insights à partir du portail Azure, cette limite est définie à 100 Go/jour. La valeur par défaut au moment de créer une ressource Application Insights à partir de Visual Studio est faible (seulement 32,3 Mo/jour) et sert à des fins de tests uniquement. Dans ce cas, l’idée est que l’utilisateur augmente la limite quotidienne avant de déployer l’application en production. La limite maximale est de 1 000 Go/jour, à moins que vous en ayez demandé une plus élevée pour les besoins d’une application à fort trafic. Définissez la limite maximale avec précaution en partant du principe que vous ne devez **jamais atteindre la limite maximale**. Vous perdriez en effet le reste de données de la journée et ne pourriez pas surveiller votre application. Pour modifier ce paramètre, utilisez l’option Limite quotidienne de volume qui est fournie dans la page des coûts d’utilisation et estimés (voir ci-dessous). Nous avons supprimé la restriction sur certains types d’abonnements qui disposent d’un crédit qui n’a pas pu être utilisé pour Application Insights. Si l’abonnement impose une limite de dépense, la boîte de dialogue Limite quotidienne indique comment la supprimer et permet d’augmenter la limite quotidienne au-delà de 32,3 Mo/jour.
* **Limitation :** ce paramètre limite le débit de données à 32 000 événements par seconde, en moyenne sur 1 minute.

*Que se passe-t-il si mon application dépasse le taux limite ?*

* Le volume de données que votre application envoie est évalué à chaque minute. S’il dépasse le taux par seconde moyen de plus d’une minute, le serveur refuse des demandes. Le Kit de développement logiciel (SDK) met les données en mémoire tampon et essaie ensuite de renvoyer, en diffusant un pic pendant plusieurs minutes. Si votre application envoie régulièrement des données au-delà du taux limite, certaines données sont supprimées. (les kits de développement logiciel (SDK) ASP.NET, Java et JavaScript essaient de renvoyer de cette manière ; les autres SDK suppriment parfois simplement les données limitées). En cas de limitation, vous en êtes informé par un avertissement.

*Comment déterminer la quantité de données envoyées par mon application ?*

* Ouvrez la page des **coûts d’utilisation et estimés** pour afficher le graphique du volume quotidien de données. 
* Ou dans Metrics Explorer, ajoutez un nouveau graphique et sélectionnez la mesure **Volume du point de données** . Basculez sur le regroupement et regroupez par **Type de données**.

## <a name="to-reduce-your-data-rate"></a>Pour réduire le débit de données
Voici quelques opérations possibles pour réduire le volume de données :

* Utilisez l’ [échantillonnage](app-insights-sampling.md). Cette technologie réduit le débit de données sans fausser vos mesures et sans impacter la capacité à naviguer entre des éléments associés dans la recherche. Dans les applications serveurs, elle s’applique automatiquement.
* [Limitez le nombre d’appels Ajax qui peuvent être signalés](app-insights-javascript.md#detailed-configuration) dans chaque affichage de page, ou désactivez les rapports Ajax.
* Désactivez les modules de collecte dont vous n'avez pas besoin en [modifiant ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Par exemple, vous pouvez décider que les compteurs de performances ou les données de dépendance ne sont pas essentiels.
* Diviser vos données de télémétrie pour séparer les clés d’instrumentation. 
* Procédez à la pré-agrégation des métriques. Si vous avez placé des appels de TrackMetric dans votre application, vous pouvez réduire le trafic en utilisant la surcharge qui accepte votre calcul de la moyenne et de l’écart type d’un lot de mesures. Une autre possibilité consiste à utiliser un [package de pré-agrégation](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Gestion du volume maximal quotidien de données

Vous pouvez utiliser le plafond de volume quotidien pour limiter les données collectées, mais, s’il est atteint, vous perdrez toute la télémétrie envoyée par votre application pour le reste de la journée. Il est **déconseillé** de laisser votre application atteindre le plafond quotidien, car vous ne pouvez pas suivre l’intégrité et les performances de votre application une fois que ce plafond est atteint.

Utilisez plutôt [l’Échantillonnage](app-insights-sampling.md) pour ajuster le volume de données au niveau souhaité, et le plafond quotidien seulement en dernier recours, dans le cas où votre application se mettrait subitement à envoyer des volumes de télémétrie largement supérieurs.

Pour modifier la limite quotidienne, accédez à la section Configurer de votre ressource Application Insights depuis la page des **coûts d’utilisation et estimés**, puis cliquez sur **Limite quotidienne**.

![Ajustement de la limite du volume quotidien des données de télémétrie](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>échantillonnage
[Sampling](app-insights-sampling.md) est une méthode vous permettant de réduire la fréquence d’envoi des données de télémétrie à votre application, tout en conservant la capacité à trouver des événements connexes lors des recherches de diagnostic, ainsi que le décompte des événements corrects.

L’échantillonnage est un moyen efficace de réduire les coûts et de respecter votre quota mensuel. L’algorithme d’échantillonnage conserve les éléments associés à la télémétrie, afin que, lorsque vous utilisez la recherche par exemple, vous puissiez trouver la demande liée à une exception spécifique. L’algorithme conserve également le décompte correct. Cela vous permet de voir les valeurs correctes des taux de demandes, des taux d’exception et des autres compteurs dans Metric Explorer.

Il existe plusieurs formes d’échantillonnage.

* [L’échantillonnage adaptatif](app-insights-sampling.md) est la méthode par défaut pour le kit de développement logiciel (SDK) ASP.NET. Il s’ajuste automatiquement au volume de données de télémétrie envoyées par votre application. Il fonctionne automatiquement dans le Kit de développement logiciel (SDK) de votre application web, afin de réduire le trafic de télémétrie sur le réseau. 
* *échantillonnage d’ingestion* est une méthode alternative qui fonctionne au niveau où les données de télémétrie issues de votre application entrent dans le service Application Insights. Il n’affecte pas le volume de télémétrie envoyé depuis votre application, mais il réduit le volume conservé par le service. Vous pouvez l’utiliser pour réduire le quota utilisé par les données de télémétrie provenant des navigateurs et d’autres kits de développement logiciel (SDK).

Pour définir l’échantillonnage d’ingestion, définissez le contrôle dans la boîte de dialogue Tarification :

![Dans la boîte de dialogue Quotas et tarification, cliquez sur la vignette Exemples et sélectionnez une fraction d’échantillonnage.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> La boîte de dialogue d’échantillonnage de données contrôle uniquement la valeur d’échantillonnage de l’ingestion. Il ne reflète pas le taux d’échantillonnage qui est appliqué par le Kit de développement logiciel (SDK) Application Insights dans votre application. Si les données de télémétrie entrantes ont déjà été échantillonnées dans le kit de développement logiciel (SDK), l’échantillonnage d’ingestion n’est pas appliqué.
>

Pour découvrir le taux d’échantillonnage réel, indépendamment de l’endroit où il a été appliqué, utilisez une [requête Analytics](app-insights-analytics.md) telle que celle-ci :

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Pour chaque enregistrement conservé, `itemCount` indique le nombre d’enregistrements d’origine qu’il représente, soit 1 + le nombre d’enregistrements précédents ignorés. 

## <a name="automation"></a>Automatisation

Vous pouvez utiliser le service de gestion de ressources Azure pour écrire un script de définition de plan tarifaire. [Découvrez comment](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Synthèse des limites

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Échantillonnage](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
