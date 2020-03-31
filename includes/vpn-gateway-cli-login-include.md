---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 74d7bd087df4b00c0bafb5ec33fbbdfa5c57b379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176903"
---
Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/) et suivez les instructions à l’écran. Pour plus d’informations sur la connexion, consultez [Bien démarrer avec Azure CLI](/cli/azure/get-started-with-azure-cli).

```azurecli
az login
```

Si vous disposez de plusieurs abonnements Azure, répertoriez les abonnements associés au compte.

```azurecli
az account list --all
```

Spécifiez l’abonnement à utiliser.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
