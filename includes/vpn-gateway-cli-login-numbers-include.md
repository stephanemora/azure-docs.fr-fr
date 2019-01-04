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
ms.openlocfilehash: cf3bbb6c45061d6f4885839fbfb7f069264fe986
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444098"
---
1. Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. Pour plus d’informations sur la connexion, consultez [Bien démarrer avec Azure CLI](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Si vous disposez de plusieurs abonnements Azure, répertoriez les abonnements associés au compte.

   ```azurecli
   az account list --all
   ```
3. Spécifiez l’abonnement à utiliser.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
