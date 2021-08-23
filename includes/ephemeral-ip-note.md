---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 03080e4518fdc1997740dd156135202dfc24f778
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179873"
---
> [!NOTE]
> Azure fournit une adresse IP d’accès sortant par défaut pour les machines virtuelles Azure qui n’ont pas d’adresse IP publique ou qui se trouvent dans le pool de back-ends d’un Azure Load Balancer de base interne. Le mécanisme d’adresse IP d’accès sortant par défaut fournit une adresse IP sortante qui n’est pas configurable. 
>
>L’adresse IP d’accès sortant par défaut est désactivée lorsqu’une adresse IP publique est attribuée à la machine virtuelle ou que celle-ci est placée dans le pool de back-ends d’un Standard Load Balancer avec ou sans règles de trafic sortant. Si une ressource de passerelle [NAT de réseau virtuel Azure](../articles/virtual-network/nat-gateway/nat-overview.md) est affectée au sous-réseau de la machine virtuelle, l’adresse IP d’accès sortant par défaut est désactivée.
>
> Pour plus d’informations sur les connexions sortantes dans Azure, consultez [Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes](../articles/load-balancer/load-balancer-outbound-connections.md).