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
ms.openlocfilehash: a22cc1f1bffb0126c4fe23bd9f28b292bfdc48a8
ms.sourcegitcommit: baed5a8884cb998138787a6ecfff46de07b8473d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "30198041"
---
Si vous rencontrez des problèmes de connexion à une machine virtuelle sur votre connexion VPN, vérifiez les points suivants :

- Vérifiez que votre connexion VPN aboutit.
- Vérifiez que vous vous connectez à l’adresse IP privée de la machine virtuelle.
- Si vous pouvez vous connecter à la machine virtuelle à l’aide de l’adresse IP privée, mais pas à l’aide du nom d’ordinateur, vérifiez que vous avez correctement configuré DNS. Pour plus d’informations sur le fonctionnement de la résolution de noms pour les machines virtuelles, consultez [Résolution de noms pour les machines virtuelles](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Lorsque vous vous connectez de point à site, vérifiez les éléments supplémentaires suivants :

- Utilisez « ipconfig » pour vérifier l’adresse IPv4 attribuée à l’adaptateur Ethernet sur l’ordinateur à partir duquel vous vous connectez. Si l’adresse IP est comprise dans la plage d’adresses du réseau virtuel auquel vous vous connectez, ou dans la plage d’adresses de votre VPNClientAddressPool, cette situation est désignée sous le terme d’espaces d’adressage qui se chevauchent. Lorsque vos espaces d’adressage se chevauchent de cette façon, le trafic réseau n’atteint pas Azure et reste sur le réseau local.
- Vérifiez que le package de configuration du client VPN a été généré après que les adresses IP du serveur DNS ont été spécifiées pour le réseau virtuel. Si vous avez mis à jour les adresses IP du serveur DNS, générez et installez un package de configuration du client VPN.

Pour plus d’informations sur la résolution d’une connexion RDP, consultez [Résoudre des problèmes de connexion Bureau à distance à une machine virtuelle](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).