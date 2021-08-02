---
title: Activer la redondance de zone pour Azure Cache pour Redis
description: Découvrez comment configurer la redondance de zone pour vos instances Azure Cache pour Redis de niveau Premium et Entreprise.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: b61e1e0b185355c06d10648f267895e819162318
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969712"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis"></a>Activer la redondance de zone pour Azure Cache pour Redis
Dans cet article, vous allez apprendre à configurer une instance Azure Cache redondante interzone à l’aide du portail Azure.

Les niveaux Standard, Premium et Entreprise d’Azure Cache pour Redis offrent une redondance intégrée en hébergeant chaque cache sur deux machines virtuelles dédiées. Même si ces machines virtuelles se trouvent dans des [domaines d’erreur et de mise à jour Azure](../virtual-machines/availability.md) distincts et hautement disponibles, elles sont sensibles aux défaillances au niveau du centre de données. Azure Cache pour Redis prend également en charge la redondance de zone dans ses niveaux Premium et Entreprise. Un cache redondant interzone s’exécute sur des machines virtuelles distribuées dans plusieurs [zones de disponibilité](../availability-zones/az-overview.md). Il offre une plus grande résilience et disponibilité.

> [!NOTE]
> Le transfert de données entre Zones de disponibilité Azure est facturé aux [tarifs de bande passante](https://azure.microsoft.com/pricing/details/bandwidth/) standard.

## <a name="prerequisites"></a>Prérequis
* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>Création d'un cache
Pour créer un cache, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Créer une ressource**.
  
1. Dans la page **Nouvelle**, sélectionnez **Bases de données**, puis **Azure Cache pour Redis**.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Sélectionnez Azure Cache pour Redis.":::
   
1. Dans la page **Général**, configurez les paramètres du nouveau cache.
   
    | Paramètre      | Valeur suggérée  | Description |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Abonnement** | Sélectionnez votre abonnement. | Abonnement sous lequel créer cette nouvelle instance d’Azure Cache pour Redis. | 
    | **Groupe de ressources** | Sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre cache et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
    | **Nom DNS** | Entrez un nom globalement unique. | Le nom du cache doit être une chaîne de 1 à 63 caractères ne contenant que des chiffres, des lettres et des traits d’union. Le nom doit commencer et se terminer par un chiffre ou une lettre, et ne peut pas contenir de traits d’union consécutifs. Le *nom d’hôte* de votre instance de cache sera *\<DNS name>.redis.cache.windows.net*. | 
    | **Lieu** | Sélectionnez un emplacement. | Choisissez une [Région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre cache. |
    | **Type de cache** | Sélectionnez un cache de [niveau Premium ou Entreprise](https://azure.microsoft.com/pricing/details/cache/). |  Le niveau tarifaire détermine la taille, les performances et les fonctionnalités disponibles pour le cache. Pour plus d’informations, consultez [Présentation du cache Azure pour Redis](cache-overview.md). |
   
1. Sur la page **Avancé**, pour un cache de niveau Premium, choisissez **Nombre de réplicas**.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="Nombre de réplicas":::

1. Sélectionnez **Zones de disponibilité**. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="Zones de disponibilité":::

1. Conservez les paramètres par défaut pour les autres options. 

    > [!NOTE]
    > La redondance de zone ne prend pas en charge la persistance AOF et ne fonctionne pas actuellement avec la géoréplication.
    >

1. Cliquez sur **Créer**. 
   
    La création du cache prend un certain temps. Vous pouvez surveiller la progression dans la page **Vue d’ensemble** du Azure Cache pour Redis. Lorsque **État** indique **En cours d’exécution**, le cache est prêt pour utilisation.
   
    > [!NOTE]
    > Les zones de disponibilité ne peuvent pas être modifiées après la création du cache.
    >

## <a name="zone-redundancy-faq"></a>FAQ sur la redondance de zone

- [Pourquoi ne puis-je pas activer la redondance de zone lors de la création d’un cache Premium ?](#why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache)
- [Pourquoi ne puis-je pas sélectionner les trois zones pendant la création du cache ?](#why-cant-i-select-all-three-zones-during-cache-create)
- [Puis-je mettre à jour mon cache Premium existant pour utiliser la redondance de zone ?](#can-i-update-my-existing-premium-cache-to-use-zone-redundancy)
- [Combien coûte la réplication de mes données entre Zones de disponibilité Azure ?](#how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones)

### <a name="why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache"></a>Pourquoi ne puis-je pas activer la redondance de zone lors de la création d’un cache Premium ?

La redondance de zone est disponible uniquement dans les régions Azure qui disposent de la fonctionnalité Zones de disponibilité. Consultez [Régions Azure avec Zones de disponibilité](../availability-zones/az-region.md#azure-services-supporting-availability-zones) pour obtenir la liste la plus récente.

### <a name="why-cant-i-select-all-three-zones-during-cache-create"></a>Pourquoi ne puis-je pas sélectionner les trois zones pendant la création du cache ?

Un cache Premium a un nœud principal et un nœud de réplica par défaut. Pour configurer la redondance de zone pour plus de deux zones de disponibilité, vous devez ajouter d’[autres réplicas](cache-how-to-multi-replicas.md) au cache que vous créez.

### <a name="can-i-update-my-existing-premium-cache-to-use-zone-redundancy"></a>Puis-je mettre à jour mon cache Premium existant pour utiliser la redondance de zone ?

Non, cela n’est pas pris en charge à l’heure actuelle.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones"></a>Combien coûte la réplication de mes données entre Zones de disponibilité Azure ?

Lors de l’utilisation de la redondance de zone, configurée avec plusieurs zones de disponibilité, les données sont répliquées du nœud de cache principal d’une zone vers les autres nœuds d’une ou de plusieurs autres zones. Les frais de transfert de données correspondent au coût de sortie du réseau des données se déplaçant dans les zones de disponibilité sélectionnées. Pour plus d'informations, consultez [Détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les fonctionnalités d’Azure Cache pour Redis.

> [!div class="nextstepaction"]
> [Niveaux de service Premium Azure Cache pour Redis](cache-overview.md#service-tiers)