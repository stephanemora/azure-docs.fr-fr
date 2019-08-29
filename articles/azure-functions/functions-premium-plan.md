---
title: Plan Azure Functions Premium (préversion) | Microsoft Docs
description: Détails et options de configuration (VNet, pas de démarrage à froid, durée d’exécution illimitée) pour le plan Azure Functions Premium.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: 2cc6493d01508d439d8dcef2d12ca1ea40632d81
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096227"
---
# <a name="azure-functions-premium-plan-preview"></a>Plan Azure Functions Premium (préversion)

Le plan Azure Functions Premium est une option d’hébergement pour les applications de fonction. Il présente des avantages tels que la connectivité réseau virtuel, l’absence de démarrage à froid et du matériel de pointe.  Plusieurs applications de fonction peuvent être déployées sur le même plan Premium, et celui-ci vous permet de configurer la taille d’instance de calcul, la taille du plan de base et taille maximale de plan.  Pour une comparaison entre le plan Premium et d’autres types de plans et d’hébergements, voir [options de mise à l’échelle et d’hébergement de fonction](functions-scale.md).

## <a name="create-a-premium-plan"></a>Créer un plan Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Vous pouvez également créer un plan Premium à l’aide de la commande [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) dans Azure CLI. L’exemple suivant crée un plan de niveau _Élastique Premium 1_ :

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

Dans cet exemple, remplacez `<RESOURCE_GROUP>` par votre groupe de ressources `<PLAN_NAME>` et par le nom de votre plan, qui doit être unique dans le groupe de ressources. Spécifiez une [`<REGION>` prise en charge](#regions). Pour créer un plan Premium qui prend en charge Linux, ajoutez l’option `--is-linux`.

Une fois le plan créé, vous pouvez utiliser [az functionapp create](/cli/azure/functionapp#az-functionapp-create) pour créer votre application de fonction. Dans le portail, le plan et l’application sont créés en même temps. 

## <a name="features"></a>Caractéristiques

Les fonctionnalités suivantes sont disponibles pour les applications de fonction déployées sur un plan Premium.

### <a name="pre-warmed-instances"></a>Instances chauffées au préalable

Si aucun événement ou exécution ne se produit aujourd’hui dans le Plan Consommation, il se peut que votre application descende en puissance jusqu’à atteindre zéro instance. Quand un nouvel événement se produit, une nouvelle instance doit être spécialisée avec votre application s’exécutant sur celle-ci.  La spécialisation d’une nouvelle instance peut prendre un certain temps en fonction de l’application.  Cette latence supplémentaire du premier appel est souvent appelée démarrage à froid d’application.

Dans le plan Premium, vous pouvez disposer de votre application chauffée au préalable sur un nombre spécifié d’instances, jusqu’à la taille minimale de votre plan.  Les instances chauffées au préalable vous permettent également de mettre à l’échelle une application avant une charge élevée. Lorsque l’application monte en charge, elle commence par se mettre à l’échelle dans les instances chauffées au préalable. Des instances supplémentaires continuent de s’ajouter en mémoire tampon et à chauffer immédiatement en vue de la prochaine opération de mise à l’échelle. Le fait de disposer d’un tampon d’instances chauffées au préalable vous permet d’éviter efficacement les latences de démarrage à froid.  Les instances chauffées au préalable caractérisent le plan Premium et vous devez conserver au moins une instance opérationnelle et disponible aussi longtemps que le plan est actif.

Vous pouvez configurer le nombre d’instances chauffées au préalable sur le portail Azure en sélectionnant votre **Function App**, en accédant à l’onglet **Fonctionnalités de la plateforme**, puis en sélectionnant les options pour **Monter en charge**. Dans la fenêtre d’édition de l’application, les instances chauffées au préalable sont spécifiques de cette application, mais les instances minimale et maximale s’appliquent au plan tout entier.

![Paramètres de mise à l’échelle élastique](./media/functions-premium-plan/scale-out.png)

Vous pouvez également configurer des instances chauffées au préalable pour une application avec Azure CLI

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Connectivité de réseau privé

Azure Functions déployée sur un plan Premium tire parti de la [nouvelle expérience d’intégration de réseau virtuel pour les applications web](../app-service/web-sites-integrate-with-vnet.md).  Une fois configurée, votre application peut communiquer avec des ressources de votre réseau virtuel ou sécurisées via des points de terminaison de service.  Des restrictions d’adresse IP sont également disponibles sur l’application pour limiter le trafic entrant.

Lors de l’attribution d’un sous-réseau à votre Function App dans un plan Premium, vous avez besoin d’un sous-réseau avec suffisamment d’adresses IP pour chaque instance potentielle. Si le nombre maximal d’instances peut varier pendant la période de préversion, nous avons besoin d’une liste d’adresses IP bloquées comprenant au moins 100 adresses disponibles.

Pour plus d’informations, voir [intégrer votre Function App avec un réseau virtuel](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Mise à l’échelle élastique rapide

Des instances de calcul supplémentaires sont automatiquement ajoutées pour votre application en utilisant la même logique de mise à l’échelle rapide que le Plan Consommation.  Pour en savoir plus sur le fonctionnement de la mise à l’échelle, voir [Échelle et hébergement dans Azure Functions](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Durée d’exécution illimitée

Azure Functions dans un Plan Consommation est limité à 10 minutes par exécution.  Dans le plan Premium, la durée d’exécution par défaut est de 30 minutes pour éviter tout perte de contrôle. Cependant, vous pouvez [modifier la configuration de host.json](./functions-host-json.md#functiontimeout) afin de rendre son exécution illimitée pour les applications du plan Premium.

En préversion, la durée d’exécution n’est pas garantie au-delà de 12 minutes et aura les meilleures chances de dépasser 30 minutes si votre application n’est pas mise à l’échelle au-delà de son nombre de workers minimal.

## <a name="plan-and-sku-settings"></a>Paramètres du plan et de la référence SKU

Lorsque vous créez le plan, vous configurez deux paramètres : le nombre minimal d’instances (ou taille du plan) et la limite maximale en rafale.  Le nombre minimal d’instances pour un plan Premium est de 1, et la limite maximale en rafale pendant la période de préversion est de 20.  Les instances minimales sont réservées et toujours opérationnelles.

> [!IMPORTANT]
> Vous êtes facturé pour chaque instance allouée en lien avec le nombre minimal d’instances, que les fonctions s’exécutent ou non.

Si votre application nécessite un nombre d’instances supérieur à la taille de votre plan, elle peut continuer à monter en charge jusqu’à ce que le nombre d’instances atteigne la limite maximale en rafale.  Vous êtes facturé pour des instances dépassant la taille de votre plan uniquement quand elles sont en cours d’exécution et louées pour vous.  Nous nous efforçons d’augmenter l’échelle votre application jusqu’à la limite maximale définie, tandis que les instances minimales du plan sont garanties pour votre application.

Vous pouvez configurer la taille et les nombres maximaux d’instances du plan via le portail Azure en sélectionnant les options **Scale Out** du plan ou une Function App déployée sur celui-ci (sous **Fonctionnalités de la plateforme**).

Vous pouvez également augmenter la limite maximale en rafale à partir d’Azure CLI :

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Références SKU d’instance disponibles

Pendant la création ou la mise à l’échelle de votre plan, vous pouvez choisir entre trois tailles d’instance.  Vous êtes facturé pour le nombre total de cœurs et la mémoire consommés par seconde.  Votre application peut monter en charge automatiquement en augmentant le nombre d’instances en fonction des besoins.  

|SKU|Cœurs|Mémoire|Stockage|
|--|--|--|--|
|EP1|1|3,5 GO|250 Go|
|EP2|2|7 GO|250 Go|
|EP3|4|14 GO|250 Go|

## <a name="regions"></a>Régions

Vous trouverez ci-dessous les régions prenant actuellement en charge la préversion publique, pour chaque système d’exploitation.

|Région| Windows | Linux |
|--| -- | -- |
|Australie Est| ✔ | |
|Sud-Australie Est | ✔ | ✔ |
|Centre du Canada| ✔ |  |
|USA Centre| ✔ |  |
|Asie Est| ✔ |  |
|USA Est | | ✔ |
|USA Est 2| ✔ |  |
|France Centre| ✔ |  |
|Japon Est|  | ✔ |
|OuJapon Est| ✔ | |
|Centre de la Corée| ✔ |  |
|Centre-Nord des États-Unis| ✔ |  |
|Europe Nord| ✔ | ✔ |
|États-Unis - partie centrale méridionale| ✔ |  |
|Inde Sud | ✔ | |
|Asie Sud-Est| ✔ | ✔ |
|Ouest du Royaume-Uni| ✔ |  |
|Europe Ouest| ✔ | ✔ |
|Inde Ouest| ✔ |  |
|USA Ouest| ✔ | ✔ |

## <a name="known-issues"></a>Problèmes connus

Vous pouvez suivre l’état des problèmes connus de la [préversion publique sur GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comprendre les options de mise à l’échelle et d’hébergement d’Azure Functions](functions-scale.md)
