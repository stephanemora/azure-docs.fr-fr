---
title: Estimation des coûts d’un plan Consommation dans Azure Functions
description: Apprenez à mieux estimer les coûts induits par l’exécution de votre application de fonction dans un plan Consommation dans Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 9544dded7516b07ad7d919d08b0b9cd4e12d0607
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567320"
---
# <a name="estimating-consumption-plan-costs"></a>Estimation des coûts d’un plan Consommation

Il existe actuellement trois types de plans d’hébergement pour une application qui s’exécute dans Azure Functions, chaque plan ayant son propre modèle de tarifs : 

| Plan | Description |
| ---- | ----------- |
| [**Consommation**](consumption-plan.md) | Vous êtes facturé uniquement pour la durée d’exécution de votre application de fonction. Ce plan comprend un [forfait gratuit][page des tarifs] par abonnement.|
| [**Premium**](functions-premium-plan.md) | Fournit les mêmes fonctionnalités et le même mécanisme de mise à l’échelle que le plan Consommation, mais avec des performances améliorées et un accès au réseau virtuel. Le coût se base sur le niveau tarifaire choisi. Pour plus d’informations, consultez [Plan Premium Azure Functions](functions-premium-plan.md). |
| [**Dédié (App Service)**](dedicated-plan.md) <br/>(niveau de base ou supérieur) | Si vous avez besoin d’exécuter sur des machines virtuelles dédiées ou en isolation, d’utiliser des images personnalisées ou si vous voulez utiliser votre capacité de plan App Service en excès. Utilise la [facturation du plan App Service standard](https://azure.microsoft.com/pricing/details/app-service/). Le coût se base sur le niveau tarifaire choisi.|

Vous avez choisi le plan qui répond le mieux à vos besoins de performance et à votre budget. Pour en savoir plus, voir [Mise à l’échelle et hébergement d’Azure Functions](functions-scale.md).

Cet article a trait uniquement au plan Consommation, car celui-ci entraîne des coûts variables. Cet article remplace l’article [Consumption plan cost billing FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

Durable Functions peut également s’exécuter dans un plan Consommation. Pour en savoir plus sur les considérations financières lors de l’utilisation de Durable Functions, consultez [Facturation Durable Functions](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Coûts d’un plan de consommation

Le *coût* de l’exécution d’une fonction unique se mesure en *Go-secondes*. Le coût d’exécution est calculé en combinant l’utilisation de sa mémoire et sa durée d’exécution. Une fonction qui s’exécute plus longtemps coûte plus cher, tout comme une fonction qui consomme plus de mémoire. 

Imaginez le cas où la quantité de mémoire utilisée par la fonction reste constante. Dans ce cas, le calcul du coût est une simple multiplication. Par exemple, supposons que votre fonction a consommé 0,5 Go pendant 3 secondes. Ainsi, le coût d’exécution s’élève à `0.5GB * 3s = 1.5 GB-seconds`. 

Étant donné que l’utilisation de la mémoire change au fil du temps, le calcul correspond au fond à l’intégrale de l’utilisation de la mémoire au fil du temps.  Le système effectue ce calcul en échantillonnant l’utilisation de la mémoire du processus (avec les processus enfants) à intervalles réguliers. Comme mentionné dans la [page des tarifs], l’utilisation de la mémoire est arrondie au compartiment de 128 Mo le plus proche. Quand votre processus utilise 160 Mo, vous êtes facturé pour 256 Mo. Le calcul prend en compte la concurrence, à savoir l’exécution de plusieurs fonctions simultanées dans le même processus.

> [!NOTE]
> Alors que l’utilisation du processeur n’est pas directement prise en compte dans le coût d’exécution, elle peut exercer un impact sur le coût quand elle affecte la durée d’exécution de la fonction.

Pour une fonction déclenchée par HTTP, quand une erreur se produit avant le début de l’exécution du code de votre fonction, vous n’êtes pas facturé pour une exécution. Cela signifie que les réponses 401 de la plateforme en raison de la validation de la clé API ou de la fonctionnalité d’authentification/d’autorisation d’App Service ne sont pas comptabilisées par rapport au coût d’exécution. De même, les réponses de code d’état 5xx ne sont pas comptabilisées lorsqu’elles se produisent dans la plateforme avant une fonction qui traite la requête. Une réponse 5xx générée par la plateforme après le début de l’exécution du code de votre fonction est toujours comptabilisée comme une exécution, même si l’erreur n’est pas déclenchée par le code de votre fonction.

## <a name="other-related-costs"></a>Autres coûts connexes

Quand vous estimez le coût global de l’exécution de vos fonctions dans un plan, quel qu’il soit, n’oubliez pas que le runtime Functions utilise plusieurs autres services Azure, facturés chacun séparément. Lors du calcul des tarifs des applications de fonction, tous les déclencheurs et toutes les liaisons que vous avez intégrés à d’autres services Azure vous obligent à créer et payer ces services supplémentaires. 

Pour les fonctions qui s’exécutent dans un plan Consommation, le coût total est le coût d’exécution de vos fonctions, auquel s’ajoute le coût de la bande passante et des services supplémentaires. 

Lorsque vous estimez les coûts globaux de votre application de fonction et des services associés, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Coût connexe | Description |
| ------------ | ----------- |
| **Compte de stockage** | Chaque application de fonction vous oblige à avoir un [compte de stockage Azure](../storage/common/storage-introduction.md#types-of-storage-accounts) universel associé, [facturé séparément](https://azure.microsoft.com/pricing/details/storage/). Ce compte est utilisé en interne par le runtime Functions, mais vous pouvez également l’utiliser pour les déclencheurs et les liaisons de stockage. Si vous n’avez pas de compte de stockage, un compte est créé pour vous lors de la création de l’application de fonction. Pour plus d’informations, consultez [Exigences pour le compte de stockage](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Functions s’appuie sur [Application Insights](../azure-monitor/app/app-insights-overview.md) pour fournir une expérience de supervision hautes performances à vos applications de fonction. Même si ce n’est pas obligatoire, il est préférable d’[activer l’intégration à Application Insights](configure-monitoring.md#enable-application-insights-integration). Un forfait gratuit de données de télémétrie est inclus chaque mois. Pour en savoir plus, consultez la [page des tarifs Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Bande passante réseau** | Vous ne payez pas le transfert de données entre les services Azure dans la même région. En revanche, des coûts peuvent être induits pour les transferts de données sortants vers une autre région ou en dehors d’Azure. Pour plus d’informations, consultez les [détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Comportements affectant la durée d’exécution

Les comportements suivants de vos fonctions peuvent exercer un impact sur la durée d’exécution :

+ **Déclencheurs et liaisons** : Le temps nécessaire pour lire l’entrée à partir de vos [liaisons de fonction](functions-triggers-bindings.md) et y écrire la sortie est compté dans la durée d’exécution. Par exemple, quand votre fonction utilise une liaison de sortie pour écrire un message dans une file d’attente de stockage Azure, votre durée d’exécution inclut le temps nécessaire à l’écriture du message dans la file d’attente, lequel est inclus dans le calcul du coût de la fonction. 

+ **Exécution asynchrone** : La durée pendant laquelle votre fonction attend les résultats d’une requête asynchrone (`await` en C#) est comptée dans la durée d’exécution. Le calcul des Go-secondes se base sur l’heure de début et de fin de la fonction, ainsi que sur l’utilisation de la mémoire pendant cette période. Ce qui se produit au cours de cette période en termes d’activité du processeur n’est pas pris en compte dans le calcul. Vous pouvez éventuellement réduire les coûts pendant les opérations asynchrones à l’aide de [Durable Functions](durable/durable-functions-overview.md). Vous n’êtes pas facturé pour le temps d’attente passé dans les fonctions d’orchestrateur.

## <a name="viewing-cost-related-data"></a>Affichage des données relatives aux coûts

Dans [votre facture](../cost-management-billing/understand/download-azure-invoice.md), vous pouvez voir les données relatives aux coûts **Nombre total d’exécutions - Fonctions** et **Durée d’exécution - Fonctions**, ainsi que les coûts réels facturés. En revanche, ces données de facture sont un agrégat mensuel correspondant à une période de facturation passée. 

### <a name="function-app-level-metrics"></a>Métriques au niveau de l’application de fonction

Pour mieux comprendre l’impact sur les coûts de vos fonctions, vous pouvez utiliser Azure Monitor pour voir les métriques relatives aux coûts en cours de génération par vos applications de fonction. 

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]

### <a name="function-level-metrics"></a>Métriques au niveau de la fonction

Les unités d’exécution de la fonction sont une combinaison de la durée d’exécution et de votre utilisation de la mémoire, ce qui en fait une mesure difficile pour comprendre l’utilisation de la mémoire. La métrique des données de mémoire n’est actuellement pas disponible par le biais d’Azure Monitor. En revanche, si vous voulez optimiser l’utilisation de la mémoire de votre application, vous pouvez utiliser les données du compteur de performances collectées par Application Insights.  

Si vous ne l’avez pas déjà fait, [activez Application Insights dans votre application de fonction](configure-monitoring.md#enable-application-insights-integration). Une fois cette intégration activée, vous pouvez [interroger ces données de télémétrie dans le portail](analyze-telemetry-data.md#query-telemetry-data). 

Vous pouvez utiliser [Azure Monitor Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) dans le [portail Azure] ou les API REST pour obtenir ces données Monitor Metrics.

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur la supervision des applications de fonction](functions-monitoring.md)

[Page des tarifs]:https://azure.microsoft.com/pricing/details/functions/
[Azure portal]: https://portal.azure.com
