---
title: Utilisation de machines virtuelles et de groupes de sécurité réseau dans Azure Bastion | Microsoft Docs
description: Cet article décrit comment incorporer des accès au groupe de sécurité réseau avec Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: e7210b2b1be072f5326070d768d5fe12c386ee0b
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191268"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Utiliser l’accès au groupe de sécurité réseau et Azure Bastion (préversion)

Lorsque vous utilisez Azure Bastion, vous pouvez utiliser des groupes de sécurité réseau (NSG). Pour plus d’informations, consultez [Groupes de sécurité](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architecture](./media/bastion-nsg/nsg_architecture.png)

Dans ce diagramme :

* l’hôte Bastion est déployé dans le réseau virtuel.
* L’utilisateur se connecte au portail Azure à l’aide de n’importe quel navigateur HTML5.
* L’utilisateur sélectionne la machine virtuelle à laquelle se connecter.
* D’un simple clic, la session RDP/SSH s’ouvre dans le navigateur.
* Aucune adresse IP publique n’est requise sur la machine virtuelle Azure.

## <a name="nsg"></a>Groupes de sécurité réseau

* **AzureBastionSubnet :** Azure Bastion est déployé dans le sous-réseau AzureBastionSubnet spécifique.  
    * **Trafic d’entrée à partir d’un réseau Internet public :** Azure Bastion créera une adresse IP publique et le port 443 devra être activé pour le trafic d’entrée de cette adresse. Il n’est PAS nécessaire d’ouvrir le port 3389/22 sur le sous-réseau AzureBastionSubnet.
    * **Trafic de sortie vers les machines virtuelles cibles :** Azure Bastion atteindra les machines virtuelles cibles via l’adresse IP privée. Les groupes de sécurité réseau doivent autoriser le trafic de sortie vers d’autres sous-réseaux de machine virtuelle cible.
* **Sous-réseau de la machine virtuelle cible :** Il s’agit du sous-réseau qui contient la machine virtuelle cible à laquelle vous souhaitez vous connecter via RDP/SSH.
    * **Trafic d’entrée à partir d’Azure Bastion :** Azure Bastion atteindra les machines virtuelles cibles via l’adresse IP privée. Les ports RDP/SSH (ports 3389 et 22, respectivement) doivent être ouverts du côté de la machine virtuelle cible sur l’adresse IP privée.

## <a name="apply"></a>Appliquer des groupes de sécurité réseau à AzureBastionSubnet

Si vous appliquez des groupes de sécurité réseau au sous-réseau **AzureBastionSubnet**, autorisez les deux balises de service suivantes pour le plan de contrôle et l’infrastructure d’Azure :

* **GatewayManager (Resource Manager uniquement)** : Cette balise désigne les préfixes d’adresse du service Azure Gateway Manager. Si vous spécifiez GatewayManager comme valeur, le trafic vers GatewayManager est autorisé ou refusé.

* **AzureCloud (Resource Manager uniquement)** : Cette balise désigne l’espace d’adressage IP pour Azure, notamment l’ensemble des adresses IP publiques du centre de données. Si vous spécifiez AzureCloud comme valeur, le trafic vers les adresses IP publiques Azure est autorisé ou refusé. Si vous souhaitez uniquement autoriser l’accès à AzureCloud dans une région spécifique, vous pouvez spécifier la région. Par exemple, pour autoriser l’accès uniquement à AzureCloud d’Azure dans la région USA Est, vous pouvez spécifier AzureCloud.EastUS en tant que balise de service.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure Bastion, consultez la [FAQ](bastion-faq.md).