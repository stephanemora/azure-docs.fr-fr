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
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246165"
---
Vous pouvez vérifier que votre connexion a réussi à l’aide de la commande [az network vpn-connection show](/cli/azure/network/vpn-connection). Dans l’exemple, «  --name » fait référence au nom de la connexion que vous voulez tester. Tout au long de l’établissement de la connexion, l’état de la connexion indique « Connexion en cours ». Une fois la connexion établie, l’état indique « Connecté ».

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```
