---
title: Appairage de réseaux virtuels et architecture d’Azure Bastion
description: Dans cet article, vous allez découvrir la possibilité d’utilisation conjointe de l’appairage de réseaux virtuels et d’Azure Bastion pour se connecter aux machines virtuelles.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 60ad4ece6de40b61b1d25e61963b30c084639eba
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108072960"
---
# <a name="vnet-peering-and-azure-bastion"></a>Appairage de réseaux virtuels et Azure Bastion

Azure Bastion et l’appairage de réseaux virtuels peuvent être utilisés ensemble. Lorsque l’appairage de réseaux virtuels est configuré, vous ne devez pas déployer Azure Bastion dans chaque réseau virtuel appairé. Cela signifie que, si vous avez un hôte Azure Bastion configuré dans un réseau virtuel, il permettre de se connecter aux machines virtuelles déployées dans un réseau virtuel appairé sans déployer d’hôte Bastion supplémentaire. Pour plus d’informations sur l’appairage de réseaux virtuels, voir [À propos de l’appairage de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md).

Azure Bastion fonctionne avec les types d’appairages suivants :

* **Peering de réseaux virtuels :** connecte des réseaux virtuels au sein d’une même région Azure.
* **Appairage global de réseaux virtuels :** connecte des réseaux virtuels entre les différentes régions Azure.

## <a name="architecture"></a>Architecture

Lorsque l’appairage de réseaux virtuels est configuré, Azure Bastion peut être déployé dans des topologies hub-and-spoke ou de maille pleine. Le déploiement Azure Bastion est effectué par réseau virtuel et non par abonnement/compte ou machine virtuelle.

Une fois que vous avez approvisionné le service Azure Bastion dans votre réseau virtuel, l’expérience RDP/SSH est disponible pour toutes vos machines virtuelles sur le même réseau virtuel, ainsi que sur les réseaux virtuels appairés. Cela signifie que vous pouvez consolider le déploiement de Bastion sur un réseau virtuel unique et toujours atteindre des machines virtuelles déployées dans un réseau virtuel appairé, centralisant ainsi le déploiement global.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagramme illustrant la conception et l’architecture":::

Cette figure représente l’architecture d’un déploiement Azure Bastion dans un modèle hub-and-spoke. Dans ce diagramme, vous pouvez voir la configuration suivante :

* L’hôte Bastion est déployé dans le réseau virtuel du hub centralisé.
* Le groupe de sécurité réseau (NSG) centralisé est déployé.
* Aucune adresse IP publique n’est requise sur la machine virtuelle Azure.

**Étapes :**

1. Connectez-vous au portail Azure à l’aide de n’importe quel navigateur HTML5.
2. Vérifiez que vous disposez d’un accès **en lecture** à la machine virtuelle cible et au réseau virtuel appairé. En outre, vérifiez sous IAM que vous disposez d’un accès en lecture aux ressources suivantes :
   * Rôle de lecteur sur la machine virtuelle.
   * Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.
   * Rôle de lecteur sur la ressource Azure Bastion.
   * Rôle de lecteur sur le réseau virtuel (inutile s’il n’y a pas de réseau virtuel appairé).
3. Pour afficher Bastion dans le menu déroulant **Connexion**, vous devez sélectionner les abonnements auxquels vous avez accès dans **Abonnement > Abonnement global**.
4. Sélectionnez la machine virtuelle à laquelle vous souhaitez vous connecter.
5. Azure Bastion est détecté en toute transparence sur le réseau virtuel appairé.
6. D’un simple clic, la session RDP/SSH s’ouvre dans le navigateur. Pour les limites de session RDP et SSH simultanées, consultez [Sessions RDP et SSH](bastion-faq.md#limits).

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Connexion":::

   Pour plus d’informations sur la connexion à une machine virtuelle via Azure Bastion, consultez :

   * [Se connecter à une machine virtuelle - RDP](bastion-connect-vm-rdp.md).
   * [Se connecter à une machine virtuelle - SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>Questions fréquentes (FAQ)

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).
