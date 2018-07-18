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
ms.openlocfilehash: 70a9e2a8f6327c0af92698b49d5b622bebba3661
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313606"
---
1. Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran. Pour plus d’informations sur la connexion, consultez [Bien démarrer avec Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

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