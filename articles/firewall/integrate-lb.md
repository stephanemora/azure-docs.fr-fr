---
title: Intégrer un pare-feu Azure avec Azure Standard Load Balancer
description: Vous pouvez intégrer un pare-feu Azure à un réseau virtuel avec Azure Standard Load Balancer (public ou interne).
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 04/14/2021
ms.author: victorh
ms.openlocfilehash: e14a8afe27fc9dd9ca40730dd7e681c3093e0b50
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505902"
---
# <a name="integrate-azure-firewall-with-azure-standard-load-balancer"></a>Intégrer un pare-feu Azure avec Azure Standard Load Balancer

Vous pouvez intégrer un pare-feu Azure à un réseau virtuel avec Azure Standard Load Balancer (public ou interne). 

La conception préconisée consiste à intégrer un équilibreur de charge interne à votre pare-feu Azure, car il s’agit d’une conception beaucoup plus simple. Vous pouvez utiliser un équilibreur de charge public si vous en avez déjà un de déployé et que vous souhaitez le conserver. Toutefois, vous devez être conscient d’un problème de routage asymétrique qui peut interrompre la fonctionnalité avec le scénario d’équilibreur de charge public.

Pour plus d’informations sur Azure Load Balancer, consultez [Qu’est-ce qu’Azure Load Balancer ?](../load-balancer/load-balancer-overview.md).

## <a name="public-load-balancer"></a>Équilibreur de charge public

Avec un équilibreur de charge public, l’équilibreur de charge est déployé avec une adresse IP publique front-end.

### <a name="asymmetric-routing"></a>Routage asymétrique

Le routage asymétrique signifie qu’un paquet suit un chemin jusqu’à la destination et en suit un autre lors du retour à la source. Ce problème se produit quand un sous-réseau dispose d’une route par défaut jusqu’à l’adresse IP privée du pare-feu et que vous utilisez un équilibreur de charge public. Dans ce cas, le trafic d’équilibreur de charge entrant est reçu par le biais de son adresse IP publique, mais le chemin de retour passe par l’adresse IP privée du pare-feu. Le pare-feu étant avec état, il supprime le paquet de retour, car le pare-feu n’a pas connaissance de l’établissement d’une session de ce type.

### <a name="fix-the-routing-issue"></a>Résoudre le problème de routage

Quand vous déployez un pare-feu Azure sur un sous-réseau, l’une des étapes consiste à créer une route par défaut pour le sous-réseau, dirigeant les paquets via l’adresse IP privée du pare-feu située sur le sous-réseau du pare-feu Azure. Pour plus d’informations, consultez [Didacticiel : Déployer et configurer un pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md#create-a-default-route)

Quand vous introduisez le pare-feu dans votre scénario d’équilibreur de charge, vous souhaitez que votre trafic Internet entre par le biais de l’adresse IP de votre pare-feu. À partir de là, le pare-feu applique ses règles et dirige les paquets (grâce à la traduction d’adresses réseau) vers l’adresse IP publique de votre équilibreur de charge. C’est là que le problème se produit. Les paquets arrivent sur l’adresse IP publique du pare-feu, mais retournent vers le pare-feu par le biais de l’adresse IP privée (à l’aide de la route par défaut).
Pour éviter ce problème, créez une route hôte supplémentaire pour l’adresse IP publique du pare-feu. Les paquets accédant à l’adresse IP publique du pare-feu sont routés via Internet. Cela évite de suivre la route par défaut jusqu’à l’adresse IP privée du pare-feu.

![Routage asymétrique](media/integrate-lb/Firewall-LB-asymmetric.png)

### <a name="route-table-example"></a>Exemple de table de route

Par exemple, les routes suivantes concernent un pare-feu à l’IP publique 20.185.97.136 et l’IP privée 10.0.1.4.

> [!div class="mx-imgBorder"]
> ![Table de routage](media/integrate-lb/route-table.png)

### <a name="nat-rule-example"></a>Exemple de règle NAT

Dans l’exemple suivant, une règle NAT traduit le trafic RDP au pare-feu à 20.185.97.136 vers l’équilibreur de charge à 20.42.98.220 :

> [!div class="mx-imgBorder"]
> ![NAT rule](media/integrate-lb/nat-rule-02.png)

### <a name="health-probes"></a>Sondes d’intégrité

N’oubliez pas que vous devez disposer d’un service web en cours d’exécution sur les hôtes du pool de l’équilibreur de charge si vous utilisez des sondes d’intégrité TCP pour le port 80 ou des sondes HTTP/HTTPS.

## <a name="internal-load-balancer"></a>Équilibreur de charge interne

Avec un équilibreur de charge interne, l’équilibreur de charge est déployé avec une adresse IP privée front-end.

Il n’y a aucun problème de routage asymétrique avec ce scénario. Les paquets entrants arrivent à l’adresse IP publique du pare-feu, subissent une traduction vers l’adresse IP privée de l’équilibreur de charge, puis retournent à l’adresse IP privée du pare-feu en suivant le même chemin de retour.

Ainsi, vous pouvez déployer ce scénario de la même manière que le scénario d’équilibreur de charge public, mais sans avoir besoin de la route hôte de l’adresse IP publique du pare-feu.

Les machines virtuelles du pool de back-ends peuvent disposer d’une connectivité Internet sortante via le pare-feu Azure. Configurez une route définie par l’utilisateur sur le sous-réseau de la machine virtuelle avec le pare-feu comme tronçon suivant.


## <a name="additional-security"></a>Sécurité supplémentaire

Pour améliorer la sécurité de votre scénario d’équilibreur de charge, vous pouvez utiliser des groupes de sécurité réseau (NSG).

Vous pouvez par exemple créer un groupe de sécurité réseau sur le sous-réseau back-end où se trouvent les machines virtuelles à charge équilibrée. Autorisez le trafic entrant en provenance de l’adresse IP/du port du pare-feu.

![Groupe de sécurité réseau](media/integrate-lb/nsg-01.png)

Pour plus d’informations sur les groupes de sécurité réseau, consultez [Groupes de sécurité](../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md).