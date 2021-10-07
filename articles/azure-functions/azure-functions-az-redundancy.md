---
title: Prise en charge des zones de disponibilité Azure Functions sur les plans Premium élastiques
description: Découvrez comment utiliser la redondance des zones de disponibilité avec Azure Functions pour les applications de fonction à haute disponibilité sur des plans Premium élastiques.
ms.topic: conceptual
ms.author: johnguo
ms.date: 09/07/2021
ms.custom: references_regions
ms.openlocfilehash: 796f4db37429e73dd1c3d50dc71b0a599977d861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700707"
---
# <a name="azure-functions-support-for-availability-zone-redundancy"></a>Prise en charge Azure Functions de la redondance des zones de disponibilité

La prise en charge des zones de disponibilité (AZ) pour Azure Functions est désormais disponible dans les plans Premium élastiques et les plans dédiés (App Service). Une application Azure Functions redondante interzone équilibre automatiquement ses instances entre les zones de disponibilité pour une plus haute disponibilité. Ce document se concentre sur la prise en charge de la redondance de zone pour les plans Premium élastiques Azure Functions. Pour en savoir plus sur la redondance de zone sur les plans dédiés, reportez-vous [ici](../app-service/how-to-zone-redundancy.md).

## <a name="overview"></a>Vue d’ensemble

Une [zone de disponibilité](../availability-zones/az-overview.md#availability-zones) est une offre à haute disponibilité qui protège vos applications et vos données contre les défaillances des centres de données. Les Zones de disponibilité sont des emplacements physiques uniques au sein d’une région Azure. Chaque zone de disponibilité est composée d’un ou de plusieurs centres de données équipés d’une alimentation, d’un système de refroidissement et d’un réseau indépendants. Pour garantir la résilience, il y a un minimum de trois zones distinctes dans toutes les régions activées. Vous pouvez créer une haute disponibilité dans votre architecture d’application en colocalisant vos ressources de calcul, de stockage, de réseau et de données dans une zone et en les répliquant dans d’autres zones.

Une application de fonction redondante dans une zone répartit automatiquement les instances sur lesquelles votre application s’exécute entre les zones de disponibilité dans la région. Pour les applications Premium élastiques redondantes dans une zone, même si l’application fait l’objet d’un scale-in et d’un scale-out, les instances sur lesquelles l’application s’exécute sont toujours réparties de manière uniforme entre les zones de disponibilité.

## <a name="requirements"></a>Configuration requise

> [!IMPORTANT]
> Quand vous sélectionnez un [compte de stockage](storage-considerations.md#storage-account-requirements) pour votre application de fonction, veillez à utiliser un [compte de stockage redondant dans une zone (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage). Sinon, en cas de défaillance d’une zone, Azure Functions peut présenter un comportement inattendu en raison de sa dépendance vis-à-vis du Stockage. 

- Windows et Linux sont tous les deux pris en charge.
- Doit être hébergé sur un plan d’hébergement [Premium élastique](functions-premium-plan.md) ou dédié. Des instructions sur la redondance de zone avec un plan d’hébergement dédié (App Service) sont disponibles [ici](../app-service/how-to-zone-redundancy.md).
  - La prise en charge des zones de disponibilité (AZ) n’est actuellement pas disponible pour les applications de fonction sur les plans de [consommation](consumption-plan.md).
- Les plans redondants dans une zone doivent spécifier au minimum 3 instances.
- De plus, les applications de fonction sur un plan Premium élastique doivent avoir au minimum 3 [instances toujours prêtes](functions-premium-plan.md#always-ready-instances).
- Peut être activé dans n’importe laquelle des régions suivantes :
  - USA Ouest 2
  - USA Ouest 3
  - USA Centre
  - USA Est
  - USA Est 2
  - Centre du Canada
  - Brésil Sud
  - Europe Nord
  - Europe Ouest
  - Allemagne Centre-Ouest
  - France Centre
  - Sud du Royaume-Uni
  - Japon Est
  - Asie Sud-Est
  - Australie Est
- À ce stade, doit être créé par le biais du [modèle ARM](../azure-resource-manager/templates/index.yml).

## <a name="how-to-deploy-a-function-app-on-a-zone-redundant-premium-plan"></a>Comment déployer une application de fonction sur un plan Premium redondant dans une zone

Pour la création initiale d’un plan Premium élastique Azure Functions redondant dans une zone, vous devez effectuer un déploiement par le biais de [modèles ARM](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md). Ensuite, une fois la création terminée, vous pouvez afficher le plan Azure Functions et interagir avec lui par le biais du portail Azure et des outils CLI. Un modèle ARM est nécessaire uniquement pour la création initiale d’un plan Azure Functions. Un guide pour l’hébergement d’Azure Functions sur les plans Premium est disponible [ici](functions-infrastructure-as-code.md#deploy-on-premium-plan). Une fois le plan redondant dans une zone créé et déployé, toute application de fonction hébergée sur votre nouveau plan est alors redondante dans une zone. 

Les seules propriétés à prendre en compte lors de la création d’un plan Azure Functions redondant dans une zone sont la nouvelle propriété **zoneRedundant** et les champs de nombre d’instances de plan Azure Functions (**capacity**). La propriété **zoneRedundant** doit être définie sur **true** et la propriété **capacity** doit être définie en fonction de l’exigence de charge de travail, mais pas inférieure à 3. Plusieurs facteurs et stratégies de haute disponibilité/de tolérance de panne déterminent le choix de la capacité appropriée. Une bonne règle de base consiste à veiller à ce que les instances soient en nombre suffisant pour l’application, de telle sorte que la perte d’une zone d’instances laisse une capacité suffisante pour gérer la charge attendue.

> [!IMPORTANT]
> Les applications de fonction Azure hébergées sur un plan Azure Functions Premium élastique redondant dans une zone doivent avoir au minimum 3 [instances toujours prêtes](functions-premium-plan.md#always-ready-instances). Cela permet d’imposer qu’une application de fonction redondante dans une zone dispose toujours d’un nombre suffisant d’instances pour satisfaire au moins un Worker par zone.

Vous trouverez ci-dessous un extrait de modèle ARM pour un plan Azure Functions Premium redondant dans une zone, qui présente le nouveau champ **zoneRedundant** et la spécification **capacity**.

```
    "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2021-01-15",
            "name": "your_plan_name_here",
            "location": "Central US",
            "sku": {
                "name": "EP3",
                "tier": "ElasticPremium",
                "size": "EP3",
                "family": "EP", 
                "capacity": 3
            },
            "kind": "elastic",
            "properties": {
                "perSiteScaling": false,
                "elasticScaleEnabled": true,
                "maximumElasticWorkerCount": 20,
                "isSpot": false,
                "reserved": false,
                "isXenon": false,
                "hyperV": false,
                "targetWorkerCount": 0,
                "targetWorkerSizeId": 0, 
                "zoneRedundant": true
            }
        }
    ]
```

Pour en savoir plus, consultez [Automatiser le déploiement de ressources pour votre application de fonction dans Azure Functions](functions-infrastructure-as-code.md).
