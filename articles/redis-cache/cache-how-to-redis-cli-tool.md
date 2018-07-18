---
title: Guide pratique pour utiliser redis-cli avec le Cache Redis Azure | Microsoft Docs
description: Découvrez comment utiliser redis-cli avec le Cache Redis Azure.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299089"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Comment utiliser l’outil en ligne de commande Redis avec le Cache Redis Azure

*redis-cli.exe* est un outil en ligne de commande populaire pour interagir avec un Cache Redis en tant que client. Cet outil est également utilisable avec le Cache Redis Azure.

Si vous souhaitez utiliser l’outil pour des plateformes Windows, vous pouvez télécharger les [outils en ligne de commande Redis pour Windows](https://github.com/MSOpenTech/redis/releases/). 

Si vous souhaitez exécuter l’outil en ligne de commande sur une autre plateforme, téléchargez le Cache Redis à partir de l’adresse [http://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Collecter les informations d’accès au cache

Vous pouvez collecter les informations nécessaires pour accéder au cache à l’aide de trois méthodes :

1. Azure CLI, en utilisant [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell, en utilisant [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. En utilisant le portail Azure

Dans cette section, vous extrairez les clés du portail Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Activer l’accès pour redis-cli.exe

Avec le Cache Redis Azure, seul le port SSL (6380) est activé par défaut. L’outil en ligne de commande `redis-cli.exe` ne prend pas en charge SSL. Vous avez deux options de configuration pour l’utiliser :

1. [Activer le port non-SSL (6379)](cache-configure.md#access-ports) - **Cette configuration n’est pas recommandée**, car, dans cette configuration, les clés d’accès sont envoyées via TCP en texte clair. Cette modification peut compromettre l’accès à votre cache. Le seul scénario dans lequel vous pouvez envisager cette configuration est quand vous accédez simplement à un cache de test.

2. Télécharger et installer [stunnel](https://www.stunnel.org/downloads.html).

    Exécutez **stunnel GUI Start** pour démarrer le serveur.

    Dans la barre des tâches, cliquez avec le bouton droit sur l’icône du serveur stunnel, puis cliquez sur **Show Log Window** (Afficher la fenêtre de journal).

    Dans le menu stunnel Log Window (Fenêtre de journal stunnel), cliquez sur **Configuration** > **Edit Configuration** (Modifier la Configuration) pour ouvrir le fichier de configuration actuel.

    Ajoutez l’entrée suivante pour *redis-cli.exe* sous la section **Service definitions** (Définitions de service). Remplacez `yourcachename` par le nom de votre cache. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Enregistrez et fermez le fichier de configuration. 
  
    Dans le menu stunnel Log Window (Fenêtre de journal stunnel), cliquez sur **Configuration** > **Reload Configuration** (Recharger la configuration).


## <a name="connect-using-the-redis-command-line-tool"></a>Se connecter à l’aide de l’outil en ligne de commande Redis

Quand vous utilisez stunnel, exécutez *redis-cli.exe* et passez uniquement vos *port* et *clé d’accès* (principale ou secondaire) pour vous connecter au cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel avec redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Si vous utilisez un cache de test avec le port non-SSL **non sécurisé**, exécutez `redis-cli.exe` et passez vos *nom d’hôte*, *port* et *clé d’accès* (principale ou secondaire) pour vous connecter au cache de test.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel avec redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Étapes suivantes

Découvrez-en plus sur l’utilisation de la [console Redis](cache-configure.md#redis-console) pour émettre des commandes.

