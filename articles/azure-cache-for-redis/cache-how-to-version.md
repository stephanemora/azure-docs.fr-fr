---
title: Définir la version de Redis pour Azure Cache pour Redis
description: Découvrez comment configurer la version de Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 41d61ccce3602b1d3e823eb6a4c46afe39159c46
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728193"
---
# <a name="set-redis-version-for-azure-cache-for-redis"></a>Définir la version de Redis pour Azure Cache pour Redis
Dans cet article, vous allez apprendre à configurer la version logicielle Redis à utiliser avec votre instance de cache. Azure Cache pour Redis offre la version principale la plus récente de ReDis et au moins une version précédente. Il mettra à jour ces versions régulièrement à mesure que de nouvelles versions de Redis seront publiées. Vous pouvez choisir entre les deux versions disponibles. N’oubliez pas que votre cache sera automatiquement mis à niveau vers la version suivante si la version qu’il utilise actuellement n’est plus prise en charge.

> [!NOTE]
> Pour le moment, Redis 6 ne prend pas en charge ACL ni la géoréplication entre un cache Redis 4 et 6.
>

## <a name="prerequisites"></a>Prérequis
* Abonnement Azure : [créez-en un gratuitement](https://azure.microsoft.com/free/)

## <a name="create-a-cache-using-the-azure-portal"></a>Créer un cache à l’aide du portail Azure
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
    | **Type de cache** | Sélectionnez [un niveau et une taille de cache](https://azure.microsoft.com/pricing/details/cache/). |  Le niveau tarifaire détermine la taille, les performances et les fonctionnalités disponibles pour le cache. Pour plus d’informations, consultez [Présentation du cache Azure pour Redis](cache-overview.md). |
   
1. Dans la page **Avancé**, choisissez une version de Redis à utiliser.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Version de Redis.":::

1. Sélectionnez **Create** (Créer). 
   
    La création du cache prend un certain temps. Vous pouvez surveiller la progression dans la page **Vue d’ensemble** du Azure Cache pour Redis. Lorsque **État** indique **En cours d’exécution**, le cache est prêt pour utilisation.


## <a name="create-a-cache-using-azure-powershell"></a>Créer un cache à l’aide d’Azure PowerShell

```azurepowershell
    New-AzRedisCache -ResourceGroupName "ResourceGroupName" -Name "CacheName" -Location "West US 2" -Size 250MB -Sku "Standard" -RedisVersion "6"
```
Pour plus d’informations sur la gestion du cache Azure pour les insertions d’Azure PowerShell, voir [ici](cache-how-to-manage-redis-cache-powershell.md)

## <a name="create-a-cache-using-azure-cli"></a>Créer un cache à l’aide d’Azure CLI

```azurecli-interactive
az redis create --resource-group resourceGroupName --name cacheName --location westus2 --sku Standard --vm-size c0 --redisVersion="6"
```
Pour plus d’informations sur la gestion du cache Azure pour les insertions d’Azure CLI, voir [ici](cli-samples.md)

## <a name="upgrade-an-existing-redis-4-cache-to-redis-6"></a>Mettre à niveau un cache Redis 4 existant vers Redis 6
Azure Cache pour Redis prend en charge la mise à niveau de la version principale du serveur de cache Redis de Redis 4 vers Redis 6. Notez que la mise à niveau est définitive et peut entraîner une courte perte de connexion. Par précaution, nous vous recommandons d’exporter les données à partir de votre cache Redis 4 existant et de tester votre application cliente avec un cache Redis 6 dans un environnement inférieur avant d’effectuer la mise à niveau. Cliquez [ici](cache-how-to-import-export-data.md) pour plus d’informations sur la procédure d’exportation.

> [!NOTE]
> Notez que la mise à niveau n’est pas prise en charge sur un cache avec un lien de géoréplication et vous devez donc dissocier manuellement vos instances de cache avant de procéder à la mise à niveau. 
>

Pour mettre à niveau votre cache, procédez comme suit :

1. Dans le Portail Azure, sélectionnez **Azure Cache pour Redis**. Puis, appuyez sur Entrée ou sélectionnez-le depuis les suggestions de la recherche.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Rechercher Azure Cache pour Redis.":::

1. Sélectionnez l’instance du cache que vous souhaitez mettre à niveau de Redis 4 vers Redis 6.

1. Sur le côté gauche de l’écran, sélectionnez **Paramètre avancé**. 

1. Si votre instance de cache est éligible à la mise à niveau, la bannière bleue suivante devrait s’afficher. Si vous souhaitez continuer, sélectionnez le texte dans la bannière.

    :::image type="content" source="media/cache-how-to-version/blue-banner-upgrade-cache.png" alt-text="Bannière bleue qui indique que vous pouvez mettre à niveau votre cache Redis 6 avec des fonctionnalités et des commandes supplémentaires qui améliorent la productivité des développeurs et la facilité d’utilisation. La mise à niveau de votre instance de cache ne peut pas être annulée.":::
    
1. Une boîte de dialogue vous avertit alors que la mise à niveau est définitive et peut entraîner une courte perte de connexion. Sélectionnez Oui si vous souhaitez mettre à niveau votre instance de cache.

    :::image type="content" source="media/cache-how-to-version/dialog-version-upgrade.png" alt-text="Boîte de dialogue contenant des informations supplémentaires sur la mise à niveau de votre cache.":::

1. Pour vérifier l’état de la mise à niveau, accédez à la **vue d’ensemble**.

    :::image type="content" source="media/cache-how-to-version/upgrade-status.png" alt-text="Vue d’ensemble indique l’état du cache en cours de mise à niveau.":::

## <a name="faq"></a>Forum aux questions

### <a name="what-features-arent-supported-with-redis-6"></a>Quelles fonctionnalités ne sont pas prises en charge avec Redis 6 ?

Pour le moment, Redis 6 ne prend pas en charge ACL ni la géoréplication entre un cache Redis 4 et 6.

### <a name="can-i-change-the-version-of-my-cache-after-its-created"></a>Puis-je modifier la version de mon cache après sa création ?

Vous pouvez mettre à niveau vos caches Redis 4 existants vers Redis 6. Pour plus d’informations, cliquez [ici](#upgrade-an-existing-redis-4-cache-to-redis-6). Notez que la mise à niveau de votre instance de cache est définitive et que vous ne pourrez plus passer vos caches Redis 6 vers Redis 4.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les fonctionnalités de Redis 6, consultez [Exploration de Redis 6.0 par Redis](https://redis.com/blog/diving-into-redis-6/)
- Pour en savoir plus sur les fonctionnalités d’Azure Cache pour Redis :

> [!div class="nextstepaction"]
> [Niveaux de service Premium Azure Cache pour Redis](cache-overview.md#service-tiers)
