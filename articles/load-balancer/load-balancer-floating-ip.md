---
title: Configuration IP flottante d’Azure Load Balancer
description: Vue d’ensemble de l’IP flottante dans Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: a72eceba85e14e058fa7c8ba2b109009e46e6df3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357456"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Configuration IP flottante d’Azure Load Balancer

L’équilibreur de charge offre plusieurs fonctionnalités pour les applications UDP et TCP.

## <a name="floating-ip"></a>IP flottante

Dans certains scénarios d’application, il est préférable ou nécessaire que plusieurs instances d’application utilisent le même port sur une machine virtuelle du pool principal. Exemples courants de réutilisation de port : 
- mise en grappe pour la haute disponibilité
- appliances virtuelles réseau
- exposition de plusieurs points de terminaison TLS sans rechiffrement. 

Si vous souhaitez réutiliser le port principal pour plusieurs règles, vous devez activer la fonctionnalité d’adresse IP flottante dans la définition des règles.

Lorsque l'IP flottante est activée, Azure modifie le mappage de l'adresse IP à l'adresse IP du frontend de l'équilibreur de charge au lieu de l'IP de l'instance backend. 

Sans IP flottante, Azure expose l'IP des instances VM. L’activation de l’adresse IP flottante modifie le mappage de l’adresse IP à l’adresse IP frontale de l’équilibreur de charge afin de permettre davantage de flexibilité. En savoir plus [ici](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Limitations

- Une adresse IP flottante n’est actuellement pas prise en charge sur des configurations IP secondaires pour des scénarios d’équilibrage de charge

## <a name="next-steps"></a>Étapes suivantes

- Pour bien démarrer avec les équilibreurs de charge, consultez [Créer un équilibreur de charge standard public](quickstart-load-balancer-standard-public-portal.md).
- Découvrez les [connexions sortantes d’Azure Load Balancer](load-balancer-outbound-connections.md).
- En savoir plus sur [Azure Load Balancer](load-balancer-overview.md).
- Découvrez les [sondes d’intégrité](load-balancer-custom-probe-overview.md).
- En savoir plus sur les [Diagnostics Load Balancer Standard](load-balancer-standard-diagnostics.md).
- En savoir plus sur les [groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md).
