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
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67176915"
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
