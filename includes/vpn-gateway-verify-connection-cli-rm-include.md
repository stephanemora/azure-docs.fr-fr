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
ms.openlocfilehash: 0e009354e66ab13cdb9fbc3cf9e4b37e904bdfd1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121190"
---
Vous pouvez vérifier que votre connexion a réussi à l’aide de la commande [az network vpn-connection show](/cli/azure/network/vpn-connection). Dans l’exemple, «  --name » fait référence au nom de la connexion que vous voulez tester. Tout au long de l’établissement de la connexion, l’état de la connexion indique « Connexion en cours ». Une fois la connexion établie, l’état indique « Connecté ».

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
