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
ms.openlocfilehash: e368b1590f263618969423d57cdf0531fc2bb54d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67176902"
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
