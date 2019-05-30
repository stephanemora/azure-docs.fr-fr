---
title: Offre des fonctions Premium Azure (version préliminaire) | Microsoft Docs
description: Plan de détails et les options de configuration (réseau virtuel, sans démarrage à froid, la durée d’exécution d’un nombre illimité) pour les fonctions Azure Premium.
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: de1915c7efb4bd8634228f341df2c57c443dd28e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242300"
---
# <a name="azure-functions-premium-plan-preview"></a>Offre des fonctions Premium Azure (version préliminaire)

Le plan Premium de fonctions Azure est une option d’hébergement pour les applications de fonction. Le plan Premium offre des fonctionnalités telles que la connectivité réseau virtuel, sans démarrage à froid et matériel de pointe.  Plusieurs applications de fonction peuvent être déployées sur le même plan Premium, et le plan vous permet de configurer la taille des instances de calcul, la taille du plan de base et taille maximale de plan.  Pour une comparaison entre le plan Premium et autres plan et l’hébergement des types, consultez [mise à l’échelle et les options d’hébergement de fonction](functions-scale.md).

> [!NOTE]
> La version préliminaire du plan Premium prend actuellement en charge les fonctions exécutées dans .NET, nœud ou Java via l’infrastructure de Windows.

## <a name="create-a-premium-plan"></a>Créer un plan Premium

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Vous pouvez également créer un plan Premium à partir de l’interface CLI Azure

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>Caractéristiques

Les fonctionnalités suivantes sont disponibles pour les applications de fonction déployées sur un plan Premium.

### <a name="pre-warmed-instances"></a>Instances préalablement chauffés

Si aucun événements et les exécutions se produisent dès aujourd'hui dans le plan consommation, votre application peut descendre en puissance aux instances de zéro. Lorsque de nouveaux événements sont effectués, une nouvelle instance doit être spécialisé avec votre application en cours d’exécution sur ce dernier.  Spécialisation de nouvelles instances peuvent prendre un certain temps en fonction de l’application.  Cette latence supplémentaire sur le premier appel est souvent appelée démarrage à froid de l’application.

Dans le plan Premium, vous pouvez avoir votre application préalable initialisée sur un nombre spécifié d’instances, jusqu'à la taille de votre plan minimale.  Instances préalablement chauffés vous permettent également de préalable mettre à l’échelle une application avant une charge élevée. Comme l’application monte en charge, il s’adapte tout d’abord dans les instances chauffés préalable. Des instances supplémentaires continuent à mettre en mémoire tampon à chaud immédiatement en préparation pour la prochaine opération de mise à l’échelle des instances. En ayant une mémoire tampon d’instances chauffés préalable, vous pouvez efficacement éviter les latences de démarrage à froid.  Instances préalablement chauffés est une fonctionnalité du plan Premium et vous devez conserver au moins une instance en cours d’exécution et disponible à tout moment le plan est actif.

Vous pouvez configurer le nombre d’instances préalablement chauffés dans Azure portail en sélectionnant votre **Function App**, permettra le **fonctionnalités de la plateforme** onglet, puis en sélectionnant le **Scale Out**options. Dans la fenêtre d’édition application (fonction), préalablement chauffés instances est spécifique à cette application, mais les instances minimales et maximales s’appliquent à la totalité du plan.

![Paramètres de mise à l’échelle élastique](./media/functions-premium-plan/scale-out.png)

Vous pouvez également configurer des instances chauffés préalable pour une application avec l’interface CLI Azure

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>Connectivité de réseau privé

Azure Functions sont déployées sur un plan Premium tire parti de [nouvelle intégration au réseau virtuel pour les applications web](../app-service/web-sites-integrate-with-vnet.md#new-vnet-integration).  En cas de configuré, votre application peut communiquer avec les ressources au sein de votre réseau virtuel ou sécurisé par le biais de points de terminaison de service.  Restrictions d’adresse IP sont également disponibles sur l’application pour limiter le trafic entrant.

Quand vous affectez un sous-réseau à votre application de fonction dans un plan Premium, vous avez besoin d’un sous-réseau avec suffisamment d’adresses IP pour chaque instance potentiel. Bien que le nombre maximal d’instances peut-être varier durant la phase préliminaire, nous avons besoin d’un bloc d’IP au moins 100 adresses disponibles.

Pour plus d’informations, consultez la rubrique [intégrer votre application de fonction à un réseau virtuel](functions-create-vnet.md).

### <a name="rapid-elastic-scale"></a>Mise à l’échelle élastique rapide

Instances de calcul supplémentaires sont automatiquement ajoutés pour votre application à l’aide de la même logique de mise à l’échelle rapide que le plan de consommation.  Pour en savoir plus sur le fonctionnement de la mise à l’échelle, consultez [mise à l’échelle et hébergement de fonction](./functions-scale.md#how-the-consumption-and-premium-plans-work).

### <a name="unbounded-run-duration"></a>Non délimité de la durée d’exécution

Azure Functions dans un plan de consommation sont limitées à 10 minutes pour une seule exécution.  Dans le plan Premium, la durée d’exécution par défaut est 30 minutes pour empêcher les exécutions de perte de contrôle. Toutefois, vous pouvez [modifier la configuration de host.json](./functions-host-json.md#functiontimeout) à faire illimitée pour les applications du plan Premium.

Dans la version préliminaire, votre durée n’est pas garantie au-delà de 12 minutes et aura les meilleures chances de s’exécuter au-delà de 30 minutes, si votre application n’est pas mis à l’échelle au-delà de son nombre minimal de travail.

## <a name="plan-and-sku-settings"></a>Paramètres de référence (SKU) et de plan

Lorsque vous créez le plan, vous configurez deux paramètres : le nombre minimal d’instances (ou la taille du plan) et la limite maximale de croissance.  Les instances minimale pour un plan Premium est 1, et la rafale maximale lors de l’aperçu est 20.  Instances minimales sont réservées et toujours en cours d’exécution.

> [!IMPORTANT]
> Vous êtes facturé pour chaque instance allouée dans le nombre minimal d’instances, peu importe si fonctions sont exécutent ou non.

Si votre application requiert des instances au-delà de la taille de votre plan, il peut continuer à augmenter jusqu'à ce que le nombre d’instances atteint la limite maximale de croissance.  Vous êtes facturé pour les instances au-delà de la taille de votre plan seule tandis que qu’ils sont en cours d’exécution et louées pour vous.  Nous apporterons efforce de mise à l’échelle votre application out à sa limite maximale définie, alors que les instances de plan minimale sont garanties pour votre application.

Vous pouvez configurer la taille du plan et sélectionné les valeurs maximales dans le portail Azure par le **Scale Out** options dans le plan ou d’une application de fonction déployée à ce plan (sous **fonctionnalités de la plateforme**).

Vous pouvez également augmenter la limite de croissance maximale à partir de l’interface CLI :

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>Instance disponible références (SKU)

Pendant la création ou mise à l’échelle de votre plan, vous pouvez choisir entre trois tailles d’instance.  Vous êtes facturé pour le nombre total de cœurs et de la mémoire consommée par seconde.  Votre application peut évoluer automatiquement à plusieurs instances en fonction des besoins.  

|SKU|Cœurs|Mémoire|Stockage|
|--|--|--|--|
|EP1|1|3,5 GO|250 Go|
|EP2|2|7 GO|250 Go|
|EP3|4|14 GO|250 Go|

## <a name="regions"></a>Régions

Voici les régions actuellement prises en charge pour la version préliminaire publique.

|Région|
|--|
|Australie Est|
|Australie Sud-Est|
|Centre du Canada|
|USA Centre|
|Asie Est|
|USA Est 2|
|France Centre|
|Japon Ouest|
|Centre de la Corée|
|USA Centre Nord|
|Europe Nord|
|USA Centre Sud|
|Inde Sud|
|Asie du Sud-Est|
|Ouest du Royaume-Uni|
|Europe Ouest|
|Inde de l’Ouest|
|USA Ouest|

## <a name="known-issues"></a>Problèmes connus

Vous pouvez suivre l’état des problèmes connus de la [version préliminaire publique sur GitHub](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Comprendre la mise à l’échelle Azure Functions et les options d’hébergement](functions-scale.md)
