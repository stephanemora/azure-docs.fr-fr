---
title: Passerelles VPN dans la VMware Solution par CloudSimple - Azure
description: En savoir plus sur les VPN de site à site CloudSimple et concepts de point-to-site VPN
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b2630614e549181f4dd2f4e79871c4594d09201
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496862"
---
# <a name="vpn-gateways-overview"></a>Vue d’ensemble des passerelles VPN

Une passerelle VPN est utilisée pour envoyer le trafic chiffré entre un réseau de la région CloudSimple à un emplacement local ou un ordinateur via l’Internet public.  Chaque région peut avoir qu’une seule passerelle VPN. Toutefois, vous pouvez créer plusieurs connexions à la même passerelle VPN. Lorsque vous créez plusieurs connexions à la même passerelle VPN, tous les tunnels VPN partagent la bande passante de passerelle disponible.

CloudSimple fournit deux types de passerelles VPN :

* Passerelle VPN de site à site
* Passerelle VPN de point-to-site

## <a name="site-to-site-vpn-gateway"></a>Passerelle VPN de site à site

Une passerelle VPN de site à site est utilisée pour envoyer le trafic chiffré entre un réseau de la région CloudSimple et un centre de données sur site. Utilisez cette connexion pour définir la plage de sous-réseaux/CIDR pour le trafic réseau entre votre réseau local et le réseau de la région CloudSimple.

La passerelle VPN vous permet d’utiliser les services en local sur votre cloud privé et des services sur votre cloud privé, à partir du réseau local.  CloudSimple fournit un serveur VPN basée sur des stratégies pour établir la connexion à partir de votre réseau local.

Cas d’utilisation de VPN de site à site sont les suivantes :

* Accessibilité de votre vCenter cloud privé à partir de toute station de travail dans votre réseau local.
* Utilisation de votre annuaire local Active Directory comme une source d’identité vCenter.
* Pratique le transfert de modèles, les fichiers ISO et les autres fichiers de machine virtuelle à partir de vos ressources locales à votre vCenter de cloud privé.
* Accessibilité des charges de travail en cours d’exécution sur votre cloud privé à partir de votre réseau local.

![Topologie de connexion VPN de site à Site](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Paramètres de chiffrement

Une connexion VPN de site à site utilise les paramètres de chiffrement par défaut suivants pour établir une connexion sécurisée.  Lorsque vous créez une connexion à partir de l’appareil VPN en local, les paramètres doivent correspondre.

Une connexion VPN de site à site utilise les paramètres de chiffrement par défaut suivants pour établir une connexion sécurisée.  Lorsque vous créez une connexion à partir de l’appareil VPN en local, utilisez un des paramètres suivants pris en charge par votre passerelle VPN.

#### <a name="phase-1-proposals"></a>Propositions de phase 1

| Paramètre | Proposition de 1 | Proposition 2 | Proposition 3 |
|-----------|------------|------------|------------|
| Version IKE | IKEv1 | IKEv1 | IKEv1 |
| Chiffrement | AES 128 | AES 256 | AES 256 |
| Algorithme de hachage| SHA 256 | SHA 256 | SHA 1 |
| Groupe Diffie Hellman (groupe Diffie-Hellman) | 1 | 1 | 1 |
| Durée de vie | 28 800 secondes | 28 800 secondes | 28 800 secondes |
| Taille des données | 4 Go | 4 Go | 4 Go |


#### <a name="phase-2-proposals"></a>Propositions de phase 2 

| Paramètre | Proposition de 1 | Proposition 2 | Proposition 3 |
|-----------|------------|------------|------------|
| Chiffrement | AES 128 | AES 256 | AES 256 |
| Algorithme de hachage| SHA 256 | SHA 256 | SHA 1 |
| Parfait avant secret groupe (PFS) | Aucun | Aucun | Aucun |
| Durée de vie | 1 800 secondes | 1 800 secondes | 1 800 secondes |
| Taille des données | 4 Go | 4 Go | 4 Go |

## <a name="point-to-site-vpn-gateway"></a>Passerelle VPN de point à site

Un VPN de point-to-site est utilisé pour envoyer du trafic chiffré entre un réseau de la région CloudSimple et un ordinateur client.  Point-to-site VPN le plus simple consiste à accéder à votre réseau de cloud privé, y compris votre vCenter de cloud privé et de la charge de travail des machines virtuelles.  Utiliser une connexion VPN de point-to-site si vous vous connectez à distance sur le cloud privé.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer la passerelle VPN](https://docs.azure.cloudsimple.com/vpn-gateway/)