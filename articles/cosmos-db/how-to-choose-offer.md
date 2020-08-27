---
title: Comment choisir l’offre de débit appropriée dans Azure Cosmos DB
description: Découvrez comment choisir entre un débit approvisionné standard (manuel) et un débit approvisionné avec mise à l’échelle automatique pour votre charge de travail.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: dech
ms.openlocfilehash: fbe17d75ad809c54939624b1409e281b2f62a037
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605213"
---
# <a name="how-to-choose-between-standard-manual-and-autoscale-provisioned-throughput"></a>Comment choisir entre le débit approvisionné standard (manuel) et le débit approvisionné avec mise à l’échelle automatique 

Azure Cosmos DB prend en charge deux types ou offres de débit approvisionné : standard (manuel) et avec mise à l’échelle automatique. Ces deux types de débit sont adaptés aux charges de travail stratégiques qui requièrent de hautes performances et à grande échelle, et s’appuient les mêmes contrats SLA Azure Cosmos DB pour le débit, la disponibilité, la latence et la cohérence.

Cet article décrit comment choisir entre le débit approvisionné standard (manuel) et celui avec mise à l’échelle automatique pour votre charge de travail. 

## <a name="overview-of-provisioned-throughput-types"></a>Vue d’ensemble des types de débits approvisionnés
Avant de se pencher sur la différence entre l’approvisionnement standard (manuel) et avec mise à l’échelle automatique, il est important de commencer par comprendre le fonctionnement du débit approvisionné dans Azure Cosmos DB. 

Lorsque vous utilisez le débit approvisionné, vous définissez le débit, mesuré en unités de requête par seconde (RU/s) requis pour votre charge de travail. Le service offre la capacité nécessaire pour prendre en charge les exigences liées au débit. Les opérations de base de données sur le service, telles que les lectures, les écritures et les requêtes, consomment un certain nombre d’unités de requête (RU). En savoir plus sur les [unités de requête](request-units.md).

Le tableau suivant présente une comparaison générale entre l’approvisionnement standard (manuel) et avec mise à l’échelle automatique.

|Description|Standard (manuel)|Mise à l’échelle automatique|
|-------------|------|-------|
|Idéale pour|Charges de travail avec trafic stable ou prévisible|Charges de travail avec trafic variable ou imprévisible. Voir [cas d’usage de la mise à l’échelle automatique](provision-throughput-autoscale.md#use-cases-of-autoscale).|
|Fonctionnement|Vous configurez une quantité définie de RU/s `T` qui ne change pas avec le temps, sauf si vous la modifiez manuellement. Chaque seconde, vous pouvez utiliser jusqu’à `T` RU/s de débit. <br/><br/>Par exemple, si vous définissez 400 RU/s standard (manuel) , le débit ne dépassera pas 400 RU/s.|Choisissez la valeur la plus élevée ou le nombre maximum de RU/s `Tmax` que vous ne souhaitez pas que le système dépasse. Le système met automatiquement à l’échelle le débit `T` de manière à ce que `0.1* Tmax <= T <= Tmax`. <br/><br/>Par exemple, si vous définissez le nombre maximum de RU/s pour la mise échelle à 4 000 RU/s, le système procédera à la mise à l’échelle entre 400-4 000 RU/s.|
|Quand utiliser cette fonctionnalité ?|Vous souhaitez gérer manuellement votre capacité de débit (RU/s) et la mettre à l’échelle vous-même.<br/><br/>Vous faites une utilisation élevée et cohérente des RU/s approvisionnées. Sur l’ensemble des heures du mois, si vous configurez un nombre de RU/s approvisionnées `T` et que vous utilisez 66 % des heures du montant total ou plus, les RU/s approvisionnées standard (manuel) vous permettrons de faire des économies.<br/><br/>Ceci se base sur une comparaison entre un total défini sur `T` en mode standard (manuel) et la même quantité `Tmax` avec la mise à l’échelle automatique. |Vous souhaitez qu’Azure Cosmos DB gère la capacité de débit (RU/s) et la mise à l’échelle en fonction de l’utilisation.<br/><br/>L’utilisation des RU/s est variable ou difficile à prédire. Sur l’ensemble des heures du mois, si vous définissez le nombre maximal de RU/s avec mise à l’échelle automatique `Tmax` et que vous utilisez la totalité de la `Tmax` pour 66 % des heures ou moins, la mise à l’échelle automatique vous permettra de faire des économies.<br/><br/>Ceci se base sur une comparaison entre sur une comparaison entre un total défini sur `Tmax` avec la mise à l’échelle automatique et la même quantité `T` en mode débit standard (manuel).|
|Modèle de facturation|La facturation s’effectue à l’heure pour les RU/s approvisionnés, quel que soit le nombre d’unités de requêtes consommées.<br/><br/>Exemple : <li>Approvisionnement de 400 RU/s</li><li>Heure 1 : aucune requête</li><li>Heure 2 : requêtes valant 400 RU/s</li><br/><br/>Pour les heures 1 et 2, vous êtes facturé 400 RU/s pour les deux heures au [tarif standard (manuel)](https://azure.microsoft.com/pricing/details/cosmos-db/).|La facturation s’effectue à l’heure, pour les RU/s les plus élevées que le système a mis à l’échelle pendant l’heure. <br/><br/>Exemple : <li>Approvisionnement avec mise à l’échelle automatique de 4 000 RU/s maximum (mise à l’échelle entre 400-4 000 RU/s)</li><li>Heure 1 : la système a été mis à l’échelle pour atteindre la valeur la plus élevée de 3 500 RU/s</li><li>Heure 2 : le système a effectué un scale-down pour atteindre un minimum de 400 RU/s (toujours 10 % de `Tmax`), à cause de l’absence d’utilisation</li><br/><br/>Vous serez facturé pour 3 500 RU/s pour l’heure 1 et 400 RU/s pour l’heure 2 avec le [débit approvisionné avec mise à l’échelle automatique](https://azure.microsoft.com/pricing/details/cosmos-db/). Le tarif pour la mise à l’échelle automatique par RU/s représente 1,5 * le tarif standard (manuel).
|Que se passe-t-il si vous dépassez les RU/s approvisionnées ?|Le nombre de RU/s reste statique par rapport à ce qui a été approvisionné. Toutes les requêtes qui consomment plus que les unités de requête approvisionnées par seconde feront l’objet d’une limitation de débit, avec une réponse qui recommande d’attendre avant d’effectuer une nouvelle tentative. Vous pouvez augmenter ou diminuer manuellement la taille les RU/s si besoin.| Le système met à l’échelle les RU/s jusqu’au nombre maximal de RU/s de la mise à l’échelle automatique. Toutes les requêtes qui consomment plus que les unités de requête approvisionnées avec mise à l’échelle automatique par seconde feront l’objet d’une limitation de débit, avec une réponse qui recommande d’attendre avant d’effectuer une nouvelle tentative.|

## <a name="understand-your-traffic-patterns"></a>Comprendre vos modèles de trafic

### <a name="new-applications"></a>Nouvelles applications ###

Si vous générez une nouvelle application et que vous ne connaissez pas encore votre modèle de trafic, vous pouvez commencer à partir du point d’entrée RU/s (ou RU/s minimum) pour éviter le surapprovisionnement au début. Autrement, si votre application est petite et n’a pas besoin d’une grande échelle, vous pourrez configurer uniquement le point d’entrée RU/s minimal pour optimiser les coûts. Pour les petites applications dont le trafic est faible, vous pouvez également envisager le mode de capacité [serverless](throughput-serverless.md).

Que vous prévoyiez d’utiliser la mise à l’échelle standard (manuelle) ou automatique, voici ce que vous devez prendre en compte :

Si vous configurez les RU/s standard (manuel) au point d’entrée de 400 RU/s, vous ne pourrez pas utiliser plus de 400 RU/s, sauf si vous modifiez le débit manuellement. Vous serez facturé pour 400 RU/s avec le débit approvisionné standard (manuel), à l’heure.

Si vous configurez le débit avec mise à l’échelle automatique au point d’entrée maximum de 4 000 RU/s, la ressource sera mise à l’échelle entre 400 et 4 000 RU/s. Étant donné que le taux de facturation du débit avec mise à l’échelle automatique par RU/s représente 1,5 x celui du débit standard (manuel), pour les heures où le système a effectué un scale-down jusqu’à la valeur minimale de 400 RU/s, votre facture sera plus élevée que si vous approvisionnez 400 RU/s manuellement. Toutefois, avec la mise à l’échelle automatique, à tout moment, en cas de pic du trafic de votre application, vous pouvez consommer jusqu’à 4 000 RU/s sans aucune intervention de l’utilisateur. De manière général, il vous faut évaluer les avantages présentés par le fait de pouvoir consommer des RU/s jusqu’à la limite maximale à tout moment grâce à la fréquence de 1,5 x la mise à l’échelle automatique.

Utilisez la [calculatrice de capacité](estimate-ru-with-capacity-planner.md) d’Azure Cosmos DB pour estimer vos besoins en débit. 

### <a name="existing-applications"></a>Applications existantes ###

Si vous disposez d’une application existante utilisant un débit approvisionné standard (manuel), vous pouvez utiliser les [métriques Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md) pour déterminer si votre modèle de trafic est adapté à la mise à l’échelle automatique. 

Tout d’abord, recherchez la [métrique de consommation d’unités de requête normalisées](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) de votre base de données ou de votre conteneur. L’utilisation normalisée mesure votre utilisation actuelle du débit approvisionné standard (manuel). Plus le nombre est proche de 100 %, plus l’usage que vous faites de vos RU/s approvisionnées est complet. [En savoir plus](monitor-normalized-request-units.md#view-the-normalized-request-unit-consumption-metric) sur la métrique.

Ensuite, déterminez comment l’utilisation normalisée va varier dans le temps. Si vous constatez que votre utilisation normalisée est variable ou imprévisible, songez à activer la mise à l’échelle automatique sur votre base de données ou conteneur. En revanche, si elle est stable et prévisible, considérez la possibilité de garder un débit approvisionné standard (manuel). 

> [!TIP]
> Avec le débit standard (manuel), vous pouvez utiliser la métrique d’utilisation normalisée pour estimer les RU/s réels que vous utiliserez potentiellement si vous basculez vers la mise à l’échelle automatique. Multipliez l’utilisation normalisée à un moment donné dans le temps par les RU/s standard approvisionnées (manuel) actuellement. Par exemple, si vous avez approvisionné 5 000 RU/s et que l’utilisation normalisée représente 90 %, l’utilisation des RU/s est de 0,9 * 5000 = 4 500 RU/s. Si vous constatez que votre modèle de trafic est variable, mais que vous avez sur ou sous-approvisionné, vous pouvez activer la mise à l’échelle automatique, puis modifier le paramètre du nombre maximal de RU/s pour la mise à l’échelle automatique en conséquence.

## <a name="measure-and-monitor-your-usage"></a>Mesurer et surveiller votre utilisation
Par la suite, une fois que vous avez choisi le type de débit, vous devez surveiller votre application et effectuer les ajustements nécessaires. 

Lorsque vous utilisez la mise à l’échelle automatique, utilisez Azure Monitor pour afficher le nombre maximal de RU/s approvisionnées avec mise à l’échelle automatique (**Débit maximal avec mise à l’échelle automatique**) et les RU/s sur lesquelles le système est actuellement mis à l’échelle (**Débit approvisionné**). Vous trouverez ci-dessous un exemple de charge de travail variable ou imprévisible utilisant la mise à l’échelle automatique. Notez qu’en l’absence de trafic, le système met à l’échelle les RU/s vers la valeur minimale de 10 % du nombre maximal de RU/s, soit respectivement 5 000 RU/s et 50 000 RU/s. 

:::image type="content" source="media/how-to-choose-offer/autoscale-metrics-azure-monitor.png" alt-text="Exemple de charge de travail utilisant la mise à l’échelle automatique":::

> [!NOTE]
> Lorsque vous utilisez un débit approvisionné standard (manuel), la métrique **Débit approvisionné** fait référence à ce que vous, en tant qu’utilisateur, avez défini. Lorsque vous utilisez le débit avec mise à l’échelle automatique, cette mesure fait référence aux RU/s sur lesquelles le système est actuellement mis à l’échelle.

## <a name="next-steps"></a>Étapes suivantes
* Utilisez la [calculatrice d’unités de requête](https://cosmos.azure.com/capacitycalculator/) pour estimer le débit de vos nouvelles charges de travail.
* Utilisez [Azure Monitor](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) pour surveiller vos charges de travail existantes.
* Découvrez comment [approvisionner le débit avec mise à l’échelle automatique sur un conteneur ou une base de données Azure Cosmos](how-to-provision-autoscale-throughput.md).
* Examinez le [FAQ sur la mise à l’échelle automatique](autoscale-faq.md).