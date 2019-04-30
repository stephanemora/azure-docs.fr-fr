---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 361d0ce5091d80198d47e4ad164f7cba8e21a55d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485273"
---
Une machine virtuelle *personnalisée* correspond simplement à une machine virtuelle créée à l’aide d’une **Application recommandée** à partir de la **Place de marché**, car elle fait l’essentiel du travail à votre place. Cependant, vous pouvez toujours effectuer des choix de configuration parmi les éléments suivants :

* Connecter la machine virtuelle à un réseau virtuel
* Installer l’agent de machine virtuelle et les extensions de machine virtuelle Azure, telles que l’extension anti-programme malveillant
* Ajouter la machine virtuelle aux services cloud existants
* Ajouter la machine virtuelle à un compte de stockage existant
* Ajouter la machine virtuelle à un groupe à haute disponibilité

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> si vous voulez que votre machine virtuelle utilise un réseau virtuel, assurez-vous de bien indiquer le réseau virtuel lorsque vous la créez.
> * Il y a deux avantages à l’utilisation d’un réseau virtuel : se connecter directement à la machine virtuelle et configurer des connexions entre différents locaux.
> * Vous pouvez configurer une machine virtuelle pour qu'elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d’informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure](../articles/virtual-network/virtual-networks-overview.md).
>
>

## <a name="to-create-the-virtual-machine"></a>Création de la machine virtuelle

<!-- Update_Description: update meta properties -->