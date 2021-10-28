---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 966232f893ef404c3c4d6e5d3bc4929f74d7bd62
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215131"
---
Pour vous connecter à un espace de travail sécurisé derrière un réseau virtuel, utilisez l’une des méthodes suivantes :

* [Passerelle VPN Azure](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md) – Connecte des réseaux locaux au réseau virtuel via une connexion privée. La connexion est établie via l’Internet public. Il existe deux types de passerelles VPN que vous pouvez utiliser :

    * [Point à site](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) : chaque ordinateur client utilise un client VPN pour se connecter au réseau virtuel.
    * [Site à site](../articles/vpn-gateway/tutorial-site-to-site-portal.md) : un périphérique VPN connecte le réseau virtuel à votre réseau local.

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/) – Connecte les réseaux locaux au cloud via une connexion privée. La connexion est établie à l’aide d’un fournisseur de connectivité.
* [Bastion Azure](../articles/bastion/bastion-overview.md) – Dans ce scénario, vous créez une machine virtuelle Azure (parfois appelée « jump box ») à l’intérieur du réseau virtuel. Vous vous connectez ensuite à la machine virtuelle à l’aide d’Azure Bastion. Bastion vous permet de vous connecter à la machine virtuelle à l’aide d’une session RDP ou SSH à partir de votre navigateur web local. Vous utilisez ensuite la jump box comme environnement de développement. Comme elle figure dans le réseau virtuel, elle peut accéder directement à l’espace de travail. Pour obtenir un exemple d’utilisation d’une machine virtuelle jumpbox, consultez [Tutoriel : Créer un espace de travail sécurisé](../articles/machine-learning/tutorial-create-secure-workspace.md).

> [!IMPORTANT]
> Quand vous utilisez une __passerelle VPN__ ou __ExpressRoute__, vous devez planifier le fonctionnement de la résolution de noms entre vos ressources locales et celles se trouvant dans le réseau virtuel. Pour plus d’informations, consultez [Utiliser un serveur DNS personnalisé](../articles/machine-learning/how-to-custom-dns.md).