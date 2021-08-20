---
title: Connecter des réseaux virtuels à l’aide de VNET Peering - tutoriel - Portail Azure
description: Dans ce tutoriel, vous apprendrez à connecter des réseaux virtuels à l’aide du peering de réseaux virtuels en utilisant le portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/06/2021
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: ca8829a5f71dabfe33f013c354e083cae08e4566
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113430631"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Tutoriel : Connecter des réseaux virtuels à l’aide du peering de réseaux virtuels en utilisant le portail Azure

Vous pouvez connecter des réseaux virtuels entre eux à l’aide du peering de réseaux virtuels. Ces réseaux virtuels peuvent appartenir à la même région ou à des régions différentes (connexion également appelée Global VNet Peering). Une fois que les deux réseaux virtuels sont appairés, leurs ressources peuvent communiquer entre elles, avec la même latence et la même bande passante que si elles se trouvaient sur le même réseau virtuel. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer deux réseaux virtuels
> * Connecter deux réseaux virtuels à l’aide du peering de réseaux virtuels
> * Déployer une machine virtuelle sur chaque réseau virtuel
> * Établir une communication entre les machines virtuelles

Si vous préférez, vous pouvez suivre ce tutoriel en utilisant [Azure CLI](tutorial-connect-virtual-networks-cli.md) ou [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous avez besoin d’un compte Azure doté d’un abonnement actif. Si vous n’en avez pas, vous pouvez créer un [compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-virtual-networks"></a>Créer des réseaux virtuels

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Dans le portail Azure, sélectionnez **Créer une ressource**.

1. Recherchez **Réseau virtuel**, puis sélectionnez **Créer**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vnet.png" alt-text="Capture d’écran de la création d’une ressource pour un réseau virtuel.":::

1. Sous l’onglet **Bases**, entrez ou sélectionnez les informations suivantes, puis acceptez les valeurs par défaut pour les autres paramètres :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement| Sélectionnez votre abonnement.|
    |Resource group| Sélectionnez **Créer** et entrez *myResourceGroup*.|
    |Région| Sélectionnez **USA Est**.|
    |Name|myVirtualNetwork1|

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-basic-tab.png" alt-text="Capture d’écran de l’onglet Informations de base pour Créer un réseau virtuel.":::

1. Sous l’onglet **Adresses IP**, entrez *10.0.0.0/16* dans le champ **Espace d’adressage**. Cliquez sur le bouton **Ajouter un sous-réseau** situé dessous, puis entrez *Subnet1* sous **Nom du sous-réseau** et *10.0.0.0/24* sous **Plage d’adresses de sous-réseau**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/ip-addresses-tab.png" alt-text="Capture d’écran de l’onglet Adresses IP pour Créer un réseau virtuel.":::

1. Sélectionnez **Vérifier + créer**, puis sélectionnez **Créer**.
   
1. Répétez les étapes 1 à 5 pour créer un deuxième réseau virtuel avec les paramètres suivants :

    | Paramètre | Valeur |
    | --- | --- |
    | Nom | myVirtualNetwork2 |
    | Espace d’adressage | 10.1.0.0/16 |
    | Resource group | Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**.|
    | Nom du sous-réseau | Sous-réseau2 |
    | Plage d’adresses de sous-réseau | 10.1.0.0/24 |

## <a name="peer-virtual-networks"></a>Appairer des réseaux virtuels

1. Dans la zone de recherche en haut du portail Azure, recherchez *myVirtualNetwork1*. Quand la mention **myVirtualNetwork1** apparaît dans les résultats de recherche, sélectionnez-la.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vnet.png" alt-text="Capture d’écran de la recherche de myVirtualNetwork1.":::

1. Sélectionnez **Appairages** sous **Paramètres**, puis sélectionnez **+ Ajouter**, comme illustré dans l’image suivante :

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-peering.png" alt-text="Capture d’écran de création d’appairages pour myVirtualNetwork1.":::
    
1. Entrez ou sélectionnez les informations suivantes, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **Ajouter**.

    | Paramètre | Valeur |
    | --- | --- |
    | Ce réseau virtuel – Nom de la liaison Peering | Nom de l’appairage de myVirtualNetwork1 au réseau virtuel distant.  *myVirtualNetwork1-myVirtualNetwork2* est utilisé pour cette connexion.|
    | Réseau virtuel distant – Nom de la liaison Peering |  Nom de l’appairage du réseau virtuel distant à myVirtualNetwork1. *myVirtualNetwork2-myVirtualNetwork1* est utilisé pour cette connexion. |
    | Abonnement | Sélectionnez votre abonnement.|
    | Réseau virtuel  | Vous pouvez sélectionner un réseau virtuel figurant dans la même région ou dans une région différente. Dans la liste déroulante, sélectionnez *myVirtualNetwork2*. |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png" alt-text="Capture d’écran de la configuration de l’appairage de réseaux virtuels." lightbox="./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional-expanded.png":::

    L’**ÉTAT DE PEERING** est *Connecté*, comme indiqué dans l’image suivante :

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png" alt-text="Capture d’écran de l’état de connexion de l’appairage de réseaux virtuels.":::

    Si vous ne voyez pas d’état *Connecté*, cliquez sur le bouton **Actualiser**.

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez une machine virtuelle sur chaque réseau virtuel afin de pouvoir tester la communication entre elles.

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

1. Dans le portail Azure, sélectionnez **Créer une ressource**.

1. Sélectionnez **Calcul**, puis **Créer** sous *Machine virtuelle*.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm.png" alt-text="Capture d’écran de la création d’une ressource pour des machines virtuelles.":::

1. Entrez ou sélectionnez les informations suivantes dans l’onglet **Informations de base**, acceptez les valeurs par défaut pour les autres paramètres, puis cliquez sur **Créer** :

    | Paramètre | Valeur |
    | --- | --- |
    | Resource group| Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**. |
    | Nom | myVm1 |
    | Emplacement | Sélectionnez **USA Est**. |
    | Image | Sélectionnez une image de système d’exploitation. Pour cette machine virtuelle, *Centre de données Windows Server 2019 – Gen1* est sélectionnée. |
    | Taille | Sélectionnez une taille de machine virtuelle. Pour cette machine virtuelle *Standard_D2s_v3* est sélectionnée. |
    | Nom d’utilisateur | Entrez un nom d’utilisateur. Le nom d’utilisateur *azure* a été choisi pour cet exemple. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-). |
   
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab.png" alt-text="Capture d’écran de la configuration de l’onglet Informations de base de la machine virtuelle." lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-basic-tab-expanded.png":::

1. Dans l’onglet **Mise en réseau**, sélectionnez les valeurs suivantes :

    | Paramètre | Valeur |
    | --- | --- |
    | Réseau virtuel | myVirtualNetwork1 : S’il n’est pas déjà sélectionné, sélectionnez **Réseau virtuel**, puis **myVirtualNetwork1**. |
    | Subnet | Subnet1 : S’il n’est pas déjà sélectionné, sélectionnez **Sous-réseau**, puis sélectionnez **Subnet1**. |
    | Aucun port d’entrée public | *Autoriser les ports sélectionnés* |
    | Sélectionner des ports d’entrée | *RDP (3389)* |

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab.png" alt-text="Capture d’écran de la configuration de l’onglet Mise en réseau de la machine virtuelle." lightbox="./media/tutorial-connect-virtual-networks-portal/create-vm-networking-tab-expanded.png":::

1. Sélectionnez **Vérifier + créer**, puis **Créer** pour démarrer le déploiement de la machine virtuelle.

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle

Répétez à nouveau les étapes 1 à 6 pour créer une deuxième machine virtuelle avec les modifications suivantes :

| Paramètre | Valeur |
| --- | --- |
| Nom | myVm2 |
| Réseau virtuel | myVirtualNetwork2 |

La création des machines virtuelles peut prendre plusieurs minutes. Attendez que les deux machines virtuelles aient été créées avant de passer aux étapes restantes.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="communicate-between-vms"></a>Établir une communication entre les machines virtuelles

1. Dans la zone de recherche située en haut du portail, recherchez *myVm1*. Quand **myVm1** apparaît dans les résultats de la recherche, sélectionnez-la.
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/search-vm.png" alt-text="Capture d’écran de la recherche de myVm1.":::

1. Pour vous connecter à la machine virtuelle, sélectionnez **Connexion**, puis sélectionnez **RDP** dans la liste déroulante. Sélectionnez **Télécharger le fichier RDP** pour télécharger le fichier de Bureau à distance.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png" alt-text="Capture d’écran du bouton Se connecter à la machine virtuelle."::: 

1. Pour vous connecter à la machine virtuelle, ouvrez le fichier RDP téléchargé. Si vous y êtes invité, sélectionnez **Connexion**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-connect.png" alt-text="Capture d’écran de l’écran de connexion du Bureau à distance.":::

1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle (il se peut que vous deviez choisir **Plus de choix**, puis **Utiliser un compte différent** pour spécifier les informations d’identification que vous avez entrées lors de la création de la machine virtuelle), puis sélectionnez **OK**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/rdp-credentials.png" alt-text="Capture d’écran de l’écran des informations d’identification RDP.":::

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Sélectionnez **Oui** pour continuer le processus de connexion.

1. Dans une étape ultérieure, un test ping sera utilisé pour communiquer avec la machine virtuelle *myVm1* depuis la machine virtuelle *myVm2*. Le test ping utilise le protocole ICMP, qui est interdit par le Pare-feu Windows par défaut. Sur la machine virtuelle *myVm1*, autorisez ICMP à travers le pare-feu Windows afin de pouvoir effectuer un test ping pour cette machine virtuelle à partir de *myVm2* lors d’une étape ultérieure, en utilisant PowerShell :

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Bien que ce test ping soit utilisé pour établir une communication entre les machines virtuelles dans ce tutoriel, il n’est pas recommandé d’autoriser le protocole IMCP via le pare-feu Windows lors de déploiements de production.

1. Pour établir une connexion avec la machine virtuelle *myVm2*, entrez la commande suivante à partir d’une invite de commandes sur la machine virtuelle *myVm1* :

    ```
    mstsc /v:10.1.0.4
    ```
    
1. Étant donné que vous avez activé la commande ping sur *myVm1*, vous pouvez désormais y effectuer un test ping par adresse IP :

    ```
    ping 10.0.0.4
    ```
    
    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/myvm2-ping-myvm1.png" alt-text="Capture d’écran de myVM2 effectuant un test ping sur myVM1.":::

1. Déconnectez vos sessions RDP sur *myVm1* et *myVm2*.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient : 

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

    :::image type="content" source="./media/tutorial-connect-virtual-networks-portal/delete-resource-group.png" alt-text="Capture d’écran de la page Supprimer un groupe de ressources.":::

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le peering de réseaux virtuels](virtual-network-peering-overview.md)
