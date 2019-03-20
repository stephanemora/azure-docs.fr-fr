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
ms.openlocfilehash: 778151e401624398b70101a242e4cf0be8e0a1b3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2019
ms.locfileid: "58114435"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Récupération du nom d’hôte, des ports et des clés d’accès à l’aide de l’interface de ligne de commande Azure

Pour récupérer le nom d’hôte et les ports à l’aide de l’interface CLI Azure que vous pouvez appeler [afficher de redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_show)et pour récupérer les clés que vous pouvez appeler [az redis liste des clés](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). Le script suivant appelle ces deux commandes et renvoie le nom d’hôte, les ports et les clés dans la console.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Pour plus d’informations sur ce script, consultez [obtenir le nom d’hôte, les ports et les clés pour le Cache Azure pour Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). Pour plus d’informations sur Azure CLI, consultez [installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et [prise en main Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
