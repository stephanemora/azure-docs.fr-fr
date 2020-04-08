---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060509"
---
## <a name="scenario"></a>Scénario

Pour mieux illustrer la configuration d’une adresse IP statique pour une machine virtuelle, ce document utilise le scénario suivant :

![Scénario de réseau virtuel : Sous-réseaux frontaux et principaux, avec une adresse IP statique pour le sous-réseau frontal](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

Dans ce scénario, vous créez une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd*, et la configurez pour utiliser l’adresse IP statique *192.168.1.101*.
