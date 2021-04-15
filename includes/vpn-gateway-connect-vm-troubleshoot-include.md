---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3f689f24c80bcb49909bd28f077dc12520b4f47e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104802"
---
Si vous rencontrez des problèmes de connexion à une machine virtuelle sur votre connexion VPN, vérifiez les points suivants :

- Vérifiez que votre connexion VPN aboutit.
- Vérifiez que vous vous connectez à l’adresse IP privée de la machine virtuelle.
- Si vous pouvez vous connecter à la machine virtuelle à l’aide de l’adresse IP privée, mais pas à l’aide du nom d’ordinateur, vérifiez que vous avez correctement configuré DNS. Pour plus d’informations sur le fonctionnement de la résolution de noms pour les machines virtuelles, consultez [Résolution de noms pour les machines virtuelles](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

Lorsque vous vous connectez de point à site, vérifiez les éléments supplémentaires suivants :

- Utilisez « ipconfig » pour vérifier l’adresse IPv4 attribuée à l’adaptateur Ethernet sur l’ordinateur à partir duquel vous vous connectez. Si l’adresse IP est comprise dans la plage d’adresses du réseau virtuel auquel vous vous connectez, ou dans la plage d’adresses de votre VPNClientAddressPool, cette situation est désignée sous le terme d’espaces d’adressage qui se chevauchent. Lorsque vos espaces d’adressage se chevauchent de cette façon, le trafic réseau n’atteint pas Azure et reste sur le réseau local.
- Vérifiez que le package de configuration du client VPN a été généré après que les adresses IP du serveur DNS ont été spécifiées pour le réseau virtuel. Si vous avez mis à jour les adresses IP du serveur DNS, générez et installez un package de configuration du client VPN.

Pour plus d’informations sur la résolution d’une connexion RDP, consultez [Résoudre des problèmes de connexion Bureau à distance à une machine virtuelle](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection).