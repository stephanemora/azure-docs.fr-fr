---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 35b4f49638d64a1de322fc712602a2d6cbad290a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019113"
---
> [!NOTE]
> Azure fournit une adresse IP d’accès sortant par défaut pour les machines virtuelles Azure qui n’ont pas d’adresse IP publique ou qui se trouvent dans le pool de back-ends d’un Azure Load Balancer de base interne. Le mécanisme d’adresse IP d’accès sortant par défaut fournit une adresse IP sortante qui n’est pas configurable. 
>
> Pour plus d’informations sur l’accès sortant par défaut, consultez [Accès sortant par défaut dans Azure](../articles/virtual-network/ip-services/default-outbound-access.md)
>
>L’adresse IP d’accès sortant par défaut est désactivée quand une adresse IP publique est affectée à la machine virtuelle, ou quand la machine virtuelle est placée dans le pool de back-ends d’un équilibreur de charge standard avec ou sans règles de trafic sortant. Si une ressource de passerelle [NAT de réseau virtuel Azure](../articles/virtual-network/nat-gateway/nat-overview.md) est affectée au sous-réseau de la machine virtuelle, l’adresse IP d’accès sortant par défaut est désactivée.
>
> Les machines virtuelles créées par des groupes de machines virtuelles identiques en mode d’orchestration flexible n’ont pas d’accès sortant par défaut.
>
> Pour plus d’informations sur les connexions sortantes dans Azure, consultez [Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes](../articles/load-balancer/load-balancer-outbound-connections.md).