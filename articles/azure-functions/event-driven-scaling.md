---
title: Mise à l’échelle basée sur les événements dans Azure Functions
description: Explique les comportements de mise à l’échelle des applications de fonction des plans Consommation et Premium.
ms.date: 10/29/2020
ms.topic: conceptual
ms.service: azure-functions
ms.openlocfilehash: ff7194b5dfda09253526aa79b52be475c131a411
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122563339"
---
# <a name="event-driven-scaling-in-azure-functions"></a>Mise à l’échelle basée sur les événements dans Azure Functions

Dans les plans Consommation et Premium, Azure Functions met à l’échelle les ressources de processeur et de mémoire en ajoutant des instances supplémentaires de l’hôte Functions. Le nombre d’instances est déterminé par le nombre d’événements qui déclenchent une fonction. 

Chaque instance de l’hôte Functions dans le plan Consommation est limitée à 1,5 Go de mémoire et un seul processeur.  Une instance de l’hôte constitue l’intégralité de la Function App, ce qui signifie que toutes les fonctions dans une Function App partagent des ressources au sein d’une instance et sont mises à l’échelle simultanément. Les applications de fonction qui partagent le même plan Consommation sont mises à l’échelle indépendamment.  Dans le plan Premium, la taille de votre plan détermine la mémoire et l’UC disponibles pour toutes les applications de ce plan, sur cette instance.  

Les fichiers de code de fonction sont stockés dans des partages Azure Files du compte de stockage principal de la fonction. Lorsque vous supprimez le compte de stockage principal de l’application de fonction, les fichiers de code de fonction sont supprimés et ne peuvent pas être récupérés.

## <a name="runtime-scaling"></a>Mise à l’échelle du runtime

Azure Functions utilise un composant appelé *contrôleur de mise à l’échelle* pour surveiller la fréquence des événements et déterminer s’il convient d’effectuer un scale-out ou un scale-in. Le contrôleur de mise à l’échelle utilise une méthode heuristique pour chaque type de déclencheur. Par exemple, si vous utilisez un déclencheur de stockage File d’attente Azure, il est mis à l’échelle en fonction de la longueur de la file d’attente et de l’âge du plus ancien message en file d’attente.

L’unité d’échelle pour Azure Functions est la Function App. Quand les instances de l’application de fonction font l’objet d’une augmentation de taille, des ressources supplémentaires sont allouées pour exécuter plusieurs instances de l’hôte Azure Functions. À l’inverse, quand la demande de calcul est réduite, le contrôleur de mise à l’échelle supprime des instances de l’hôte de fonction. Le nombre d’instances est finalement réduit à zéro si aucune fonction n’est exécutée dans une application de fonction.

![Contrôleur de mise à l’échelle surveillant les événements et créant des instances](./media/functions-scale/central-listener.png)

## <a name="cold-start"></a>Démarrage à froid

Une fois que votre application de fonction a été inactive pendant quelques minutes, la plateforme peut effectuer un scale-down à zéro du nombre d’instances sur lesquelles votre application s’exécute. La prochaine requête présente la latence supplémentaire de la mise à l’échelle de zéro à un. Cette latence est appelée _démarrage à froid_. Le nombre de dépendances requises par votre application de fonction peut avoir un impact sur le temps de démarrage à froid. Le démarrage à froid est plus problématique pour les opérations synchrones, telles que les déclencheurs HTTP, qui doivent retourner une réponse. Si les démarrages à froid ont un impact sur vos fonctions, envisagez l’exécution dans un plan Premium ou dans un plan Dedicated avec le paramètre **Always-on** activé.   

## <a name="understanding-scaling-behaviors"></a>Présentation des comportements de mise à l’échelle

La mise à l’échelle peut varier en fonction de certains facteurs et selon le déclencheur et le langage sélectionnés. Il est nécessaire de connaître certaines subtilités relatives aux comportements de mise à l’échelle :

* **Nombre maximal d’instances :** Une application de fonction peut faire l’objet d’un scale-out jusqu’à 200 instances au maximum. Une seule instance, par contre, peut traiter plusieurs messages ou requêtes à la fois, ainsi il n’y a pas de limite définie sur le nombre d’exécutions simultanées.  Vous pouvez [spécifier une valeur maximale inférieure](#limit-scale-out) pour limiter l’échelle en fonction des besoins.
* **Taux de nouvelles instances :** Pour les déclencheurs HTTP, de nouvelles instances sont allouées, au plus, une fois par seconde. Pour les déclencheurs non HTTP, de nouvelles instances sont allouées, au plus, une fois toutes les 30 secondes. La mise à l’échelle est plus rapide lors de l’exécution dans [plan Premium](functions-premium-plan.md).
* **Efficacité de la mise à l’échelle :** Pour les déclencheurs Service Bus, utilisez des _Droits de gestion_ sur les ressources pour une mise à l’échelle d’une efficacité optimale. Avec des _Droits d’écoute_, la mise à l’échelle n’est pas aussi précise parce que la longueur de la file d’attente ne peut pas être utilisée pour informer des décisions de mise à l’échelle. Pour en savoir plus sur la définition de droits dans les stratégies d’accès Service Bus, consultez [Stratégie d’autorisation d’accès partagé](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies). Pour les déclencheurs Event Hub, consultez les [conseils de mise à l’échelle](functions-bindings-event-hubs-trigger.md#scaling) dans l’article de référence. 

## <a name="limit-scale-out"></a>Limiter le scale-out

Vous souhaiterez peut-être limiter le nombre maximal d’instances utilisées par une application pour effectuer un scale-out.  C’est le cas le plus fréquent lorsqu’un composant en aval comme une base de données a un débit limité.  Par défaut, les fonctions du plan Consommation effectuent un scale-out jusqu’à 200 instances, et les fonctions du plan Premium jusqu’à 100 instances.  Vous pouvez spécifier une valeur maximale inférieure pour une application spécifique en modifiant la valeur `functionAppScaleLimit`.  Le `functionAppScaleLimit` peut être défini sur `0` ou sur `null` pour une utilisation sans restriction, ou sur une valeur valide comprise entre `1` et le maximum de l’application.

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <RESOURCE_GROUP> -n <FUNCTION_APP-NAME>/config/web --set properties.functionAppScaleLimit=<SCALE_LIMIT>
```

```azurepowershell
$resource = Get-AzResource -ResourceType Microsoft.Web/sites -ResourceGroupName <RESOURCE_GROUP> -Name <FUNCTION_APP-NAME>/config/web
$resource.Properties.functionAppScaleLimit = <SCALE_LIMIT>
$resource | Set-AzResource -Force
```

## <a name="best-practices-and-patterns-for-scalable-apps"></a>Bonnes pratiques et modèles pour les applications scalables

Nombreux sont les aspects d’une application de fonction qui impactent sa mise à l’échelle, notamment la configuration de l’hôte, l’empreinte du runtime et l’efficacité des ressources.  Pour plus d’informations, consultez la [section sur l’extensibilité dans l’article Considérations relatives aux performances](functions-best-practices.md#scalability-best-practices). Vous devez également savoir ce qu’il se passe au niveau des connexions lors de la mise à l’échelle de votre application de fonction. Pour plus d’informations, consultez [How to manage connections in Azure Functions](manage-connections.md) (Comment gérer des connexions dans Azure Functions).

Pour plus d’informations sur la mise à l’échelle en Python et Node.js, consultez le [Guide des développeurs Python sur Azure Functions - Mise à l’échelle et concurrence](functions-reference-python.md#scaling-and-performance) et le [Guide des développeurs Node.js sur Azure Functions - Mise à l’échelle et concurrence](functions-reference-node.md#scaling-and-concurrency).

## <a name="billing-model"></a>Modèle de facturation

La facturation des différents plans est décrite en détail dans la [page Tarification d’Azure Functions](https://azure.microsoft.com/pricing/details/functions/). L’utilisation est agrégée au niveau de l’application de fonction et compte uniquement la durée d’exécution du code de fonction. Les unités de facturation sont les suivantes :

* **Consommation des ressources en gigaoctet/seconde (Go/s)** . Calcul effectué d’après une combinaison de la taille de la mémoire et de la durée d’exécution pour toutes les fonctions d’une application de fonction. 
* **Exécutions**. Comptées chaque fois qu’une fonction est exécutée en réponse à un déclencheur d’événements.

Vous trouverez des requêtes et des informations utiles pour vous aider à comprendre votre facture de consommation [dans la FAQ sur la facturation](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="next-steps"></a>Étapes suivantes

+ [Options d’hébergement Azure Functions](functions-scale.md)

