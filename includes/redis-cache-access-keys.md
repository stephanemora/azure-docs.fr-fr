---
title: Fichier Include
description: Fichier Include
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720381"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Récupération du nom d’hôte, des ports et des clés d’accès à partir du portail Azure

Pour vous connecter à une instance Azure Cache pour Redis, les clients de cache ont besoin du nom d’hôte, des ports et d’une clé pour le cache. Certains clients peuvent référencer ces éléments par des noms légèrement différents. Vous pouvez récupérer le nom d’hôte, les ports et les clés d’accès à partir du [portail Azure](https://portal.azure.com).

- Pour récupérer les clés d’accès, sélectionnez **Clés d’accès** dans la barre de navigation de gauche du cache. 
  
  ![Clés du cache Azure pour Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Pour récupérer le nom d’hôte et les ports, sélectionnez **Propriétés** dans la barre de navigation de gauche du cache. Le nom d'hôte prend la forme *\<Nom DNS>.redis.cache.windows.net*.

  ![Propriétés du cache Azure pour Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

