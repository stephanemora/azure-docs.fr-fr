---
title: Fichier include
description: Fichier include
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: ae9526a47a77118503274fc824538980ebcd50de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91828820"
---
1. Pour créer un cache, connectez-vous au [portail Azure](https://portal.azure.com), puis sélectionnez **Créer une ressource**.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Sélectionnez Azure Cache pour Redis.":::

   
1. Dans la page **Nouvelle**, sélectionnez **Bases de données**, puis **Azure Cache pour Redis**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Sélectionnez Azure Cache pour Redis.":::
   
1. Dans la page **Nouveau cache Redis**, configurez les paramètres du nouveau cache.
   
   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nom DNS** | Entrez un nom globalement unique. | Le nom du cache doit être une chaîne de 1 à 63 caractères ne contenant que des chiffres, des lettres et des traits d’union. Le nom doit commencer et se terminer par un chiffre ou une lettre, et ne peut pas contenir de traits d’union consécutifs. Le *nom d’hôte* de votre instance de cache sera *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Dans la liste déroulante, sélectionnez votre abonnement. | Abonnement sous lequel créer cette nouvelle instance d’Azure Cache pour Redis. | 
   | **Groupe de ressources** | Dans la liste déroulante, sélectionnez un groupe de ressources ou choisissez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre cache et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. | 
   | **Lieu** | Dans la liste déroulante, sélectionnez un emplacement. | Choisissez une [Région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre cache. |
   | **Niveau tarifaire** | Sélectionnez un [Niveau tarifaire](https://azure.microsoft.com/pricing/details/cache/). |  Le niveau tarifaire détermine la taille, les performances et les fonctionnalités disponibles pour le cache. Pour plus d’informations, consultez [Présentation du cache Azure pour Redis](../articles/azure-cache-for-redis/cache-overview.md). |

1. Sélectionnez l’onglet **Réseau** ou cliquez sur le bouton **Réseau** au bas de la page.

1. Sous l’onglet **Réseau**, sélectionnez votre méthode de connectivité.

1. Sélectionnez l’onglet **Suivant : Avancé** ou cliquez sur le bouton **Suivant : Avancé** en bas de la page.

1. Sous l’onglet **Avancé** d’une instance de cache de base ou standard, sélectionnez Activer/désactiver si vous souhaitez activer un port non-TLS.

1. Sous l’onglet **Avancé** d’une instance de cache premium, configurez les paramètres pour le port non-TLS, le clustering et la persistance des données.

1. Sélectionnez l’onglet **Suivant : Étiquettes** ou cliquez sur le bouton **Suivant : Étiquettes** au bas de la page.

1. Si vous le voulez, sous l’onglet **Étiquettes**, entrez le nom et la valeur si vous souhaitez catégoriser la ressource. 

1. Sélectionnez **Vérifier + créer**. Vous êtes redirigé vers l’onglet Vérifier + créer où Azure valide votre configuration.

1. Une fois que le message vert Validation réussie s’affiche, sélectionnez **Créer**.

La création du cache prend un certain temps. Vous pouvez surveiller la progression dans la page **Vue d’ensemble** d’Azure Cache pour Redis. Lorsque **État** indique **En cours d’exécution**, le cache est prêt pour utilisation. 