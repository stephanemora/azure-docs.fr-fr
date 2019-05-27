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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121058"
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
