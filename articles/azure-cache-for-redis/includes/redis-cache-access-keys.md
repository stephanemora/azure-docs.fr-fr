---
title: Fichier include
description: Fichier include
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: c1f5aae79daf4cf8fb3a447eba5538212e3dc327
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533784"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Récupération du nom d’hôte, des ports et des clés d’accès à partir du portail Azure

Pour vous connecter à une instance Azure Cache pour Redis, les clients de cache ont besoin du nom d’hôte, des ports et d’une clé pour le cache. Certains clients peuvent référencer ces éléments par des noms légèrement différents. Vous pouvez récupérer le nom d’hôte, les ports et les clés d’accès à partir du [portail Azure](https://portal.azure.com).

- Pour récupérer les clés d’accès, sélectionnez **Clés d’accès** dans la barre de navigation de gauche du cache. 
  
  ![Clés du cache Azure pour Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Pour récupérer le nom d’hôte et les ports, sélectionnez **Propriétés** dans la barre de navigation de gauche du cache. Le nom d’hôte prend la forme *\<DNS name>.redis.cache.windows.net*.

  ![Propriétés du cache Azure pour Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

