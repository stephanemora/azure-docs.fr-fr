---
title: Azure VMware Solutions (AVS) – Passerelles VPN
description: Apprenez-en davantage sur les concepts de VPN site à site et point à site AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 73171e2c46bdf6c934db5777efe36ba51153a686
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024855"
---
# <a name="vpn-gateways-overview"></a>Vue d’ensemble des passerelles VPN

Une passerelle VPN est utilisée pour acheminer du trafic chiffré d’un réseau régional AVS vers un emplacement local ou un ordinateur via l’Internet public. Une région ne peut posséder qu’une seule passerelle VPN qui prend en charge plusieurs connexions. Lorsque vous créez plusieurs connexions à la même passerelle VPN, tous les tunnels VPN partagent la bande passante de passerelle disponible.

AVS fournit deux types de passerelles VPN :

* Passerelle VPN de site à site
* Passerelle VPN de point à site

## <a name="site-to-site-vpn-gateway"></a>Passerelle VPN de site à site

Une passerelle VPN site à site est utilisée pour acheminer le trafic chiffré d’un réseau régional AVS vers un centre de données local. Utilisez cette connexion pour définir la plage de sous-réseaux/CIDR pour le trafic entre votre réseau local et le réseau régional AVS.

La passerelle VPN vous permet d’utiliser des services locaux sur votre cloud privé AVS, et des services sur votre cloud privé AVS à partir du réseau local. AVS fournit un serveur VPN basé sur une stratégie pour établir une connexion à partir de votre réseau local.

Les cas d’utilisation de VPN de site à site sont les suivants :

* Accessibilité de votre vCenter de cloud privé AVS à partir de toute station de travail dans votre réseau local.
* Utilisation de votre Active Directory local comme source d’identité vCenter.
* Transfert pratique de modèles de machines virtuelles, d’ISO et d’autres fichiers à partir de vos ressources locales vers votre vCenter de cloud privé AVS.
* Accessibilité des charges de travail s’exécutant sur votre cloud privé AVS à partir de votre réseau local.

![Topologie d’une connexion VPN de site à site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Paramètres de chiffrement

Une connexion VPN de site à site utilise les paramètres de chiffrement par défaut suivants pour établir une connexion sécurisée. Lorsque vous créez une connexion à partir de votre appareil VPN local, utilisez un des paramètres suivants pris en charge par votre passerelle VPN.

#### <a name="phase-1-proposals"></a>Propositions de phase 1

| Paramètre | Proposition 1 | Proposition 2 | Proposition 3 |
|-----------|------------|------------|------------|
| Version IKE | IKEv1 | IKEv1 | IKEv1 |
| Chiffrement | AES 128 | AES 256 | AES 256 |
| Algorithme de hachage| SHA 256 | SHA 256 | SHA 1 |
| Groupe Diffie Hellman (groupe DH) | 2 | 2 | 2 |
| Durée de vie | 28 800 secondes | 28 800 secondes | 28 800 secondes |
| Taille des données | 4 Go | 4 Go | 4 Go |

#### <a name="phase-2-proposals"></a>Propositions de phase 2

| Paramètre | Proposition 1 | Proposition 2 | Proposition 3 |
|-----------|------------|------------|------------|
| Chiffrement | AES 128 | AES 256 | AES 256 |
| Algorithme de hachage| SHA 256 | SHA 256 | SHA 1 |
| Groupe PFS (Perfect Forward Secrecy) | None | None | None |
| Durée de vie | 1 800 secondes | 1 800 secondes | 1 800 secondes |
| Taille des données | 4 Go | 4 Go | 4 Go |


> [!IMPORTANT]
> Définissez le clamping du MSS TCP sur 1200 sur votre périphérique VPN. Dans le cas où vos périphériques VPN ne prendraient pas en charge le réglage de la taille maximale de segment, vous pouvez à la place définir l’unité de transmission maximale dans l’interface de tunnel sur 1240 octets.

## <a name="point-to-site-vpn-gateway"></a>Passerelle VPN de point à site

Une passerelle VPN point à site est utilisée pour envoyer le trafic chiffré d’un réseau régional AVS à un ordinateur client. Un VPN point-à-Site est le moyen le plus facile d’accéder à votre réseau de cloud privé AVS, y compris à votre vCenter de cloud privé et à vos machines virtuelles de charge de travail AVS. Utilisez une connexion VPN point à site si vous vous connectez au cloud privé AVS à distance.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer la passerelle VPN](vpn-gateway.md)
