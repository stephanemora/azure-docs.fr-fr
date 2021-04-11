---
title: 'Démarrage rapide : Créer un cache Redis Enterprise'
description: Dans ce guide de démarrage rapide, vous allez découvrir comment créer une instance Azure Cache pour Redis dans les niveaux Enterprise
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 8b450ddff0952777652a957ba2ed554a4bc6497d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584820"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>Démarrage rapide : Créer un cache Redis Enterprise

Les niveaux Enterprise d’Azure Cache pour Redis offrent des instances [Redis Enterprise](https://redislabs.com/redis-enterprise/) entièrement intégrées et gérées. Ces différents niveaux sont :
* Enterprise, qui utilise la mémoire volatile (DRAM) sur une machine virtuelle pour stocker les données
* Enterprise Flash, qui utilise à la fois la mémoire volatile et la mémoire non volatile (NVMe ou SSD) pour stocker les données.

## <a name="prerequisites"></a>Prérequis

Vous aurez besoin d’un abonnement Azure avant de commencer. Si vous n’en avez pas, créez un [compte](https://azure.microsoft.com/). Pour plus d’informations, consultez [Considérations spéciales relatives aux niveaux Enterprise](cache-overview.md#special-considerations-for-enterprise-tiers).

## <a name="create-a-cache"></a>Création d'un cache
1. Pour créer un cache, connectez-vous au portail Azure, puis sélectionnez **Créer une ressource**.

1. Dans la page **Nouvelle**, sélectionnez **Bases de données**, puis **Azure Cache pour Redis**.
   
   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Sélectionner Azure Cache pour Redis":::
   
1. Dans la page **Nouveau cache Redis**, configurez les paramètres du nouveau cache.
   
   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement sous lequel créer cette nouvelle instance d’Azure Cache pour Redis. | 
   | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre cache et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
   | **Nom DNS** | Entrez un nom globalement unique. | Le nom du cache doit être une chaîne de 1 à 63 caractères ne contenant que des chiffres, des lettres et des traits d’union. Le nom doit commencer et se terminer par un chiffre ou une lettre, et ne peut pas contenir de traits d’union consécutifs. Le *nom d’hôte* de votre instance de cache sera *\<DNS name>.<Azure region>.redisenterprise.cache.azure.net*. | 
   | **Lieu** | Dans la liste déroulante, sélectionnez un emplacement. | Les niveaux Enterprise sont disponibles dans les régions Azure sélectionnées. |
   | **Type de cache** | Dans la liste déroulante, sélectionnez un niveau *Enterprise* ou *Enterprise Flash* et une taille. |  Le niveau détermine la taille, les performances et les fonctionnalités disponibles pour le cache. |
   
   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="Onglet Informations de base pour le niveau Enterprise":::

   > [!NOTE] 
   > Veillez à cocher la case sous « Conditions » avant de continuer.
   >

1. Sélectionnez **Suivant : Mise en réseau** et ignorez.

1. Sélectionnez **Suivant : Avancé**, puis définissez **Stratégie de clustering** sur **Entreprise**. Activez **Accès non-TLS uniquement** si vous envisagez de vous connecter au nouveau cache sans utiliser TLS. Toutefois, cette option n’est pas recommandée.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="Onglet Avancé pour le niveau Enterprise":::

1. Sélectionnez **Suivant : Étiquettes** et ignorez.

1. Sélectionnez **Suivant : Vérifier + créer**.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="Onglet Vérifier + créer pour le niveau Enterprise":::

1. Vérifiez les paramètres, puis cliquez sur **Créer**.
   
   La création du cache prend un certain temps. Vous pouvez surveiller la progression dans la page **Vue d’ensemble** du Azure Cache pour Redis. Lorsque **État** indique **En cours d’exécution**, le cache est prêt pour utilisation.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer une instance de niveau Entreprise pour Azure Cache pour Redis.

> [!div class="nextstepaction"]
> [Créer une application web ASP.NET qui utilise un cache Azure pour Redis.](./cache-web-app-howto.md)

