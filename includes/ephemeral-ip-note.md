---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 04/08/2021
ms.author: allensu
ms.openlocfilehash: 062cdb7c442ee5556aa454c978bd4aa47058aaf5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107529563"
---
> [!NOTE]
> Azure fournit une adresse IP éphémère pour les machines virtuelles Azure qui n’ont pas d’adresse IP publique ou qui se trouvent dans le pool de back-ends d’un Azure Load Balancer de base interne. Le mécanisme d’adresse IP éphémère fournit une adresse IP sortante qui n’est pas configurable. 
>
>L’adresse IP éphémère est désactivée lorsqu’une adresse IP publique est attribuée à la machine virtuelle ou que celle-ci est placée dans le pool de back-ends d’un Standard Load Balancer avec ou sans règles de trafic sortant. Si une ressource de passerelle [NAT de réseau virtuel Azure](../articles/virtual-network/nat-overview.md) est affectée au sous-réseau de la machine virtuelle, l’adresse IP éphémère est désactivée.
>
> Pour plus d’informations sur les connexions sortantes dans Azure, consultez [Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes](../articles/load-balancer/load-balancer-outbound-connections.md).