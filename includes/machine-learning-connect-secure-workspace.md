---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 3bd72c4930c510df03856ad31bbea51d21829c1e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646102"
---
Pour vous connecter à un espace de travail sécurisé derrière un réseau virtuel, utilisez l’une des méthodes suivantes :

* [Passerelle VPN Azure](/azure/vpn-gateway/vpn-gateway-about-vpngateways) – Connecte des réseaux locaux au réseau virtuel via une connexion privée. La connexion est établie via l’Internet public. Il existe deux types de passerelles VPN que vous pouvez utiliser :

    * [Point à site](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) : chaque ordinateur client utilise un client VPN pour se connecter au réseau virtuel.
    * [Site à site](/azure/vpn-gateway/tutorial-site-to-site-portal) : un périphérique VPN connecte le réseau virtuel à votre réseau local.

* [ExpressRoute](https://azure.microsoft.com/services/expressroute/) – Connecte les réseaux locaux au cloud via une connexion privée. La connexion est établie à l’aide d’un fournisseur de connectivité.
* [Bastion Azure](/azure/bastion/bastion-overview) – Dans ce scénario, vous créez une machine virtuelle Azure (parfois appelée « jump box ») à l’intérieur du réseau virtuel. Vous vous connectez ensuite à la machine virtuelle à l’aide d’Azure Bastion. Bastion vous permet de vous connecter à la machine virtuelle à l’aide d’une session RDP ou SSH à partir de votre navigateur web local. Vous utilisez ensuite la jump box comme environnement de développement. Comme elle figure dans le réseau virtuel, elle peut accéder directement à l’espace de travail. Pour obtenir un exemple d’utilisation d’une machine virtuelle jumpbox, consultez [Tutoriel : Créer un espace de travail sécurisé](/azure/machine-learning/tutorial-create-secure-workspace).

> [!IMPORTANT]
> Quand vous utilisez une __passerelle VPN__ ou __ExpressRoute__, vous devez planifier le fonctionnement de la résolution de noms entre vos ressources locales et celles se trouvant dans le réseau virtuel. Pour plus d’informations, consultez [Utiliser un serveur DNS personnalisé](/azure/machine-learning/how-to-custom-dns).
