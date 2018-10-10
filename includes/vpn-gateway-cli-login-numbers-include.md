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
ms.openlocfilehash: 82de8eab089e5f666e1a2ce4eab09bfd2895185b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47020079"
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
