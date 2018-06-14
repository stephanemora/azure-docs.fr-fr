---
title: Fichier Include
description: Fichier Include
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: e035b49d9e386287baf67bba756f7b58a764acc5
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34173116"
---
Pour créer un cache, connectez-vous au [portail Azure](https://portal.azure.com), puis cliquez sur **Créer une ressource** > **Bases de données** > **Cache Redis**.

![New cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

Dans **Nouveau cache Redis**, configurez les paramètres du nouveau cache.

| Paramètre      | Valeur suggérée  | Description |
| ------------ |  ------- | -------------------------------------------------- |
| **Nom DNS** | Nom globalement unique | Le nom du cache doit être une chaîne de 1 à 63 caractères et contenir uniquement des chiffres, des lettres et le caractère `-` . Le nom du cache ne peut ni commencer ni se terminer par le caractère `-` et il n’accepte pas de caractères `-` consécutifs.  | 
| **Abonnement** | Votre abonnement | Abonnement sous lequel est créé ce nouveau Cache Redis Azure. | 
| **Groupe de ressources** |  *TestResources* | Nom du nouveau groupe de ressources dans lequel créer votre cache. En plaçant toutes les ressources d’une application dans un groupe, vous pouvez les gérer ensemble. Par exemple, si vous supprimez le groupe de ressources, vous supprimez également toutes les ressources associées à l’application. | 
| **Lieu** | Est des États-Unis | Choisissez une [région](https://azure.microsoft.com/regions/) à proximité des autres services qui utilisent votre cache. |
| **[Niveau tarifaire](https://azure.microsoft.com/pricing/details/cache/)** |  De base C0 (cache de 250 Mo) |  Le niveau tarifaire détermine la taille, les performances et les fonctionnalités disponibles pour le cache. Pour plus d’informations, consultez [Azure Redis Cache Overview](../articles/redis-cache/cache-overview.md) (Vue d’ensemble de Cache Redis Azure). |
| **Épingler au tableau de bord** |  Volumes sélectionnés | Épinglez le nouveau cache à votre tableau de bord pour en faciliter la recherche. |

![Create cache](media/redis-cache-create/redis-cache-cache-create.png) 

Une fois les paramètres du nouveau cache configurés, cliquez sur **Créer**. 

La création du cache peut prendre plusieurs minutes. Pour vérifier l’état d’avancement de l’opération, vous pouvez consulter le tableau de bord. Après sa création, le nouveau cache indique l’état **En cours d’exécution**, et il est prêt à être utilisé.

![Cache created](media/redis-cache-create/redis-cache-cache-created.png)

