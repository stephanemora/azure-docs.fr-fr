---
title: Créer une machine virtuelle avec une adresse IP privée statique – Portail Azure
description: Découvrez comment créer une machine virtuelle avec une adresse IP privée statique à l’aide du portail Azure.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 10/01/2021
ms.custom: template-how-to
ms.openlocfilehash: eec4e9e07f19130732219c0932f66a1cfa0fa2b7
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368379"
---
# <a name="create-a-virtual-machine-with-a-static-private-ip-address-using-the-azure-portal"></a>Créer une machine virtuelle avec une adresse IP privée statique à l’aide du portail Azure

Une adresse IP privée est automatiquement assignée à une machine virtuelle (VM) Azure, dans une plage que vous définissez. Cette plage est basée sur le sous-réseau dans lequel la machine virtuelle est déployée. Cette adresse est conservée jusqu’à ce que la machine virtuelle soit supprimée. Azure attribue dynamiquement l’adresse IP privée disponible suivante du sous-réseau dans lequel vous créez une machine virtuelle. Assignez une adresse IP statique à la machine virtuelle si vous souhaitez assigner une adresse IP spécifique du sous-réseau.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Procédez comme suit pour créer une machine virtuelle, un réseau virtuel et un sous-réseau.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

3. Dans **Machines virtuelles**, sélectionnez **+ Créer**, puis **+ Machine virtuelle**.

4. Dans **Créer une machine virtuelle**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**. </br> Entrez **myResourceGroup** dans **Nom**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom de la machine virtuelle | Entrez **myVM**. |
    | Région | Sélectionnez **(États-Unis) USA Est 2**. |
    | Options de disponibilité | Sélectionnez **Aucune redondance d’infrastructure requise**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter – Gen2**. |
    | Instance Azure Spot | Laissez la case non activée. |
    | Taille | Sélectionnez une taille. |
    | **Compte administrateur** |   |
    | Nom d’utilisateur | Entrez un nom d’utilisateur. |
    | Mot de passe | Entrez un mot de passe. |
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **RDP (3389)** |

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/create-vm.png" alt-text="Capture d’écran de la création d’une machine virtuelle.":::

    > [!WARNING]
    > Le port 3389 est sélectionné pour autoriser l’accès à distance à la machine virtuelle Windows Server depuis Internet. Il est déconseillé d’ouvrir le port 3389 à Internet pour gérer les charges de travail de production. </br> Pour un accès sécurisé aux machines virtuelles Azure, consultez **[Qu’est-ce qu’Azure Bastion ?](../../bastion/bastion-overview.md)**

3. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
4. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre | Valeur |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | Acceptez le nom réseau par défaut. |
    | Subnet | Acceptez la configuration de sous-réseau par défaut. |
    | Adresse IP publique | Acceptez la configuration d’IP publique par défaut. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **De base** |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **RDP (3389)** |

5. Sélectionnez **Revoir + créer**. 
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="change-private-ip-address-to-static"></a>Passer une adresse IP privée en statique

Dans cette section, vous allez faire passer l’adresse IP privée de **dynamique** à **statique** pour la machine virtuelle que vous avez créée précédemment.

1. Dans la zone de recherche située en haut du portail, entrez **Machine virtuelle**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Dans **Machines virtuelles**, sélectionnez **myVM**.

3. Sélectionnez **Mise en réseau** dans les **Paramètres** de **myVM**.

4. Dans **Mise en réseau**, sélectionnez le nom de l’interface réseau en regard d’**Interface réseau**. Dans cet exemple, le nom de la carte réseau est **myvm472**.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-nic.png" alt-text="Capture d’écran de la sélection de l’interface réseau.":::

5. Dans les propriétés de l’interface réseau, sélectionnez **Configurations IP** dans **Paramètres**.

6. Sélectionnez **ipconfig1** dans la page **Configurations IP**.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-ip-configuration.png" alt-text="Capture d’écran de la sélection de la configuration IP.":::

7. Dans **Attribution**, sélectionnez **Statique**. Sélectionnez **Enregistrer**.

    :::image type="content" source="./media/virtual-networks-static-private-ip-arm-pportal/select-static-assignment.png" alt-text="Capture d’écran de la sélection de l’attribution statique.":::

    > [!NOTE]
    > Si, après avoir sélectionné **Enregistrer**, vous remarquez que l’affectation est toujours définie sur **Dynamique**, cela signifie que l’adresse IP que vous avez tapée est déjà utilisée. Essayez une autre adresse IP.

Pour rétablir l’adresse IP dynamique, définissez l’attribution de votre adresse IP privée sur **Dynamique**, puis sélectionnez **Enregistrer**.

> [!WARNING]
> À partir du système d’exploitation d’une machine virtuelle, vous ne devez pas affecter de manière statique l’adresse IP *privée* qui est affectée à la machine virtuelle Azure. N’effectuez l’affectation statique d’une adresse IP privée qu’en cas de nécessité, par exemple quand vous [affectez de nombreuses adresses IP à des machines virtuelles](virtual-network-multiple-ip-addresses-portal.md). 
>
>Si vous définissez manuellement l’adresse IP privée dans le système d’exploitation, assurez-vous qu’elle correspond à l’adresse IP privée affectée à l’[interface réseau](virtual-network-network-interface-addresses.md#change-ip-address-settings) Azure. Sinon, vous risquez de perdre la connectivité à la machine virtuelle. En savoir plus sur les paramètres [d’adresse IP privée](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le, ainsi que toutes les ressources qu’il contient :

1. Entrez **myResourceGroup** dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.

2. Sélectionnez **Supprimer le groupe de ressources**.

3. Entrez **myResourceGroup** dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail les [adresses IP publiques statiques](public-ip-addresses.md#ip-address-assignment).

- En savoir plus sur les [adresses IP publiques](public-ip-addresses.md#public-ip-addresses) dans Azure.

- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).

- En savoir plus sur les [adresses IP privées](private-ip-addresses.md) et l’affectation d’une [adresse IP privée statique](virtual-network-network-interface-addresses.md#add-ip-addresses) à une machine virtuelle Azure.

