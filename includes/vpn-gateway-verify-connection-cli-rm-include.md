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
ms.openlocfilehash: 1c3103889bd11df45710cc0d6afaeeba022c9ace
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38763003"
---
Vous pouvez vérifier que votre connexion a réussi à l’aide de la commande [az network vpn-connection show](/cli/azure/network/vpn-connection#show). Dans l’exemple, «  --name » fait référence au nom de la connexion que vous voulez tester. Tout au long de l’établissement de la connexion, l’état de la connexion indique « Connexion en cours ». Une fois la connexion établie, l’état indique « Connecté ».

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```