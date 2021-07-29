---
title: Utilisation de machines virtuelles et de groupes de sécurité réseau dans Azure Bastion
description: Découvrez comment utiliser des groupes de sécurité réseau avec Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 84f32755a4838fbcb29b3d85d8308b5288d746ea
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110537902"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Utiliser l’accès au groupe de sécurité réseau et Azure Bastion

Lorsque vous utilisez Azure Bastion, vous pouvez utiliser des groupes de sécurité réseau (NSG). Pour plus d’informations, consultez [Groupes de sécurité](../virtual-network/network-security-groups-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="Groupe de sécurité réseau":::

Dans ce diagramme :

* l’hôte Bastion est déployé dans le réseau virtuel.
* L’utilisateur se connecte au portail Azure à l’aide de n’importe quel navigateur HTML5.
* L’utilisateur accède à la machine virtuelle Azure avec un protocole RDP/SSH.
* Intégration de la connexion : session RDP/SSH en un clic dans le navigateur
* Aucune adresse IP publique n’est requise sur la machine virtuelle Azure.

## <a name="network-security-groups"></a><a name="nsg"></a>Groupes de sécurité réseau

Cette section décrit le trafic réseau entre l’utilisateur et Azure Bastion et jusqu’aux machines virtuelles cibles de votre réseau virtuel :

> [!IMPORTANT]
> Si vous choisissez d’utiliser un groupe de sécurité réseau avec votre ressource Azure Bastion, vous **devez** créer toutes les règles de trafic entrant et sortant suivantes. L’omission de l’une des règles suivantes dans votre groupe de sécurité réseau empêchera votre ressource Azure Bastion de recevoir les futures mises à jour requises, et exposera par conséquent votre ressource aux vulnérabilités de sécurité.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion est spécifiquement déployé dans le sous-réseau ***AzureBastionSubnet***.

* **Trafic d’entrée :**

   * **Trafic d’entrée à partir d’un réseau Internet public :** Azure Bastion créera une adresse IP publique et le port 443 devra être activé pour le trafic d’entrée de cette adresse. Il n’est PAS nécessaire d’ouvrir le port 3389/22 sur le sous-réseau AzureBastionSubnet.
   * **Trafic d’entrée à partir du plan de contrôle d’Azure Bastion :** pour la connectivité du plan de contrôle, activez le port 443 pour un accès entrant à partir de l’étiquette de service **GatewayManager**. Ceci permet au plan de contrôle, c’est-à-dire au gestionnaire de passerelle, de communiquer avec Azure Bastion.
   * **Trafic d’entrée à partir du plan de données d’Azure Bastion :** Pour la communication du plan de données entre les composants sous-jacents d’Azure Bastion, activez les ports 8080, 5701 entrants à partir de l’étiquette de service **VirtualNetwork** vers l’étiquette de service **VirtualNetwork**. Cela permet aux composants d’Azure Bastion de communiquer entre eux.
   * **Trafic d’entrée à partir d’Azure Load Balancer :** Pour les sondes d’intégrité, activez le port 443 entrant à partir de la balise de service **AzureLoadBalancer**. Azure Load Balancer peut ainsi détecter la connectivité


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Capture d’écran montrant des règles de sécurité de trafic entrant pour la connectivité Azure Bastion.":::

* **Trafic de sortie :**

   * **Trafic de sortie vers les machines virtuelles cibles :** Azure Bastion atteindra les machines virtuelles cibles via l’adresse IP privée. Les groupes de sécurité réseau doivent autoriser le trafic de sortie vers d’autres sous-réseaux de machines virtuelles cibles sur les ports 3389 et 22.
   * **Trafic de sortie vers le plan de données d’Azure Bastion :** Pour la communication du plan de données entre les composants sous-jacents d’Azure Bastion, activez les ports 8080, 5701 sortants à partir de l’étiquette de service **VirtualNetwork** vers l’étiquette de service **VirtualNetwork**. Cela permet aux composants d’Azure Bastion de communiquer entre eux.
   * **Trafic de sortie vers d’autres points de terminaison publics dans Azure :** Azure Bastion doit pouvoir se connecter à différents points de terminaison publics dans Azure (par exemple, pour stocker les journaux de diagnostic et de mesure). Azure Bastion a donc besoin d’un accès sortant sur le port 443 vers l’étiquette de service **AzureCloud**.
   * **Trafic de sortie vers Internet :** Azure Bastion doit pouvoir communiquer avec Internet pour valider les sessions et les certificats. Nous vous recommandons donc d’activer le port 80 sortant vers **Internet**.


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Capture d’écran montrant les règles de sécurité de trafic sortant pour la connectivité Azure Bastion.":::

### <a name="target-vm-subnet"></a>Sous-réseau de la machine virtuelle cible
Il s’agit du sous-réseau qui contient la machine virtuelle cible à laquelle vous souhaitez vous connecter via RDP/SSH.

   * **Trafic d’entrée à partir d’Azure Bastion :** Azure Bastion atteindra les machines virtuelles cibles via l’adresse IP privée. Les ports RDP et SSH (3389 et 22 respectivement) doivent être ouverts du côté de la machine virtuelle cible sur l’adresse IP privée. Une bonne pratique consiste à ajouter la plage d’adresses IP du sous-réseau Azure Bastion à cette règle pour autoriser Bastion à ouvrir ces ports sur les machines virtuelles cibles de votre sous-réseau de machines virtuelles cible uniquement.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Bastion, consultez les [Questions fréquentes (FAQ)](bastion-faq.md).
