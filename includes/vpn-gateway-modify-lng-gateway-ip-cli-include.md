---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60652852"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Pour modifier la passerelle de réseau local « gatewayIpAddress »

Si le périphérique VPN auquel vous souhaitez vous connecter a changé son adresse IP publique, vous devez modifier la passerelle de réseau local pour refléter cette modification. L’adresse IP de passerelle peut être modifiée sans supprimer de connexion de passerelle VPN existante (si vous en possédez une). Pour modifier l’adresse IP de passerelle, remplacez les valeurs « Site2 » et « TestRG1 » par vos propres valeurs à l’aide de la commande [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Vérifiez que l’adresse IP est correcte dans la sortie :

```
"gatewayIpAddress": "23.99.222.170",
```
