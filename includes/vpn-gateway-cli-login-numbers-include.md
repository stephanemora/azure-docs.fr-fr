---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 52f828b9ba7bd286184b44a3d843c2cf8c75c592
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92755859"
---
1. Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/) et suivez les instructions à l’écran. Pour plus d’informations sur la connexion, consultez [Bien démarrer avec Azure CLI](/cli/azure/get-started-with-azure-cli).

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
