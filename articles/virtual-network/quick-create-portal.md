---
title: 'Démarrage rapide : Créer un réseau virtuel - Portail Azure'
titleSuffix: Azure Virtual Network
description: Dans ce guide de démarrage rapide, découvrez comment créer un réseau virtuel à l’aide du portail Azure.
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: kumud
ms.openlocfilehash: 8af5b302e3ec790b6ee9356aca0699d0edcd284e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606063"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Démarrage rapide : Créer un réseau virtuel au moyen du portail Azure

Dans ce démarrage rapide, vous découvrez comment créer un réseau virtuel à l’aide du portail Azure. Vous déployez deux machines virtuelles. Ensuite, vous établissez une communication sécurisée entre les machines virtuelles et vous vous connectez aux machines virtuelles à partir d’Internet. Un réseau virtuel est le bloc de construction fondamental de votre réseau privé dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer de manière sécurisée entre elles et avec Internet.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Créer un réseau virtuel

1. Sélectionnez **Créer une ressource** en haut à gauche du portail.

2. Dans la zone de recherche, entrez **Réseau virtuel**. Dans les résultats de la recherche, sélectionnez **Réseau virtuel**.

3. Dans la page **Réseau virtuel**, sélectionnez **Créer**.

4. Dans **Créer un réseau virtuel**, entrez ou sélectionnez les informations suivantes sous l’onglet **Général** :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer nouveau**.  </br> Entrez **myResourceGroup**. </br> Sélectionnez **OK**. |
    | **Détails de l’instance** |   |
    | Nom | Entrez **myVNet**. |
    | Région | Sélectionnez **(États-Unis) USA Est**. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Créer un réseau virtuel - Portail Azure" border="true":::

5. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

6. Dans **Espace d’adressage IPv4**, sélectionnez l’espace d’adressage existant et remplacez-le par **10.1.0.0/16**.

7. Sélectionnez **+ Ajouter un sous-réseau**, puis entrez **MySubnet** pour **Nom de sous-réseau** et **10.1.0.0/24** pour la **Plage d’adresses de sous-réseau**.

8. Sélectionnez **Ajouter**.

9. Sélectionnez l’onglet **Sécurité**, ou sélectionnez le bouton **Suivant : Sécurité** situé au bas de la page.

10. Sous **BastionHost**, sélectionnez **Activer**. Entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du bastion | Entrez **myBastionHost** |
    | Espace d’adressage AzureBastionSubnet | Entrez **10.1.1.0/24** |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Pour **Nom**, entrez **myBastionIP**. </br> Sélectionnez **OK**. |

11. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

12. Sélectionnez **Create** (Créer).

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel :

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**. 
   
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionner **myResourceGroup** |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVM1** |
    | Région | Sélectionnez **(États-Unis) USA Est** |
    | Options de disponibilité | Sélectionnez **Aucune redondance d’infrastructure requise**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Sélectionnez **Non** |
    | Taille | Choisissez la taille de la machine virtuelle ou acceptez le paramètre par défaut |
    | **Compte administrateur** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur |
    | Mot de passe | Entrez un mot de passe |
    | Confirmer le mot de passe | Entrez à nouveau le mot de passe |
    | **Règles des ports d’entrée** |    |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |
    |

3. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
4. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre | Valeur |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Subnet | Sélectionnez **mySubnet** |
    | Adresse IP publique | Sélectionnez **Aucun** |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **De base**|
    | Réseau des ports entrants publics | Sélectionnez **Aucun**. |
   
5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**. 
   
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionner **myResourceGroup** |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVM2** |
    | Région | Sélectionnez **(États-Unis) USA Est** |
    | Options de disponibilité | Sélectionnez **Aucune redondance d’infrastructure requise**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter** |
    | Instance Azure Spot | Sélectionnez **Non** |
    | Taille | Choisissez la taille de la machine virtuelle ou acceptez le paramètre par défaut |
    | **Compte administrateur** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur |
    | Mot de passe | Entrez un mot de passe |
    | Confirmer le mot de passe | Entrez à nouveau le mot de passe |
    | **Règles des ports d’entrée** |    |
    | Aucun port d’entrée public | Sélectionnez **Aucun**. |
    |

3. Sélectionnez l'onglet **Mise en réseau** ou choisissez **Suivant : Disques**, puis **Suivant : Mise en réseau**.
  
4. Sous l’onglet Réseau, sélectionnez ou entrez :

    | Paramètre | Valeur |
    |-|-|
    | **Interface réseau** |  |
    | Réseau virtuel | Sélectionnez **myVNet**. |
    | Subnet | Sélectionnez **mySubnet** |
    | Adresse IP publique | Sélectionnez **Aucun** |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **De base**|
    | Réseau des ports entrants publics | Sélectionnez **Aucun**. |
   
5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

## <a name="connect-to-myvm1"></a>Se connecter à myVM1

1. Accédez au [portail Azure](https://portal.azure.com) pour gérer votre machine virtuelle privée. Recherchez et sélectionnez **Machines virtuelles**.

2. Choisissez le nom de votre machine virtuelle privée **myVM1**.

3. Dans la barre de menus de la machine virtuelle, sélectionnez **Se connecter**, puis sélectionner **Bastion**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Se connecter à myVM1 avec Azure Bastion" border="true":::

4. Dans la page **Se connecter**, sélectionnez le bouton bleu **Utiliser Bastion**.

5. Dans la page **Bastion**, entrez le nom d’utilisateur et le mot de passe que vous avez créés pour la machine virtuelle.

6. Sélectionnez **Connecter**.

## <a name="communicate-between-vms"></a>Établir une communication entre les machines virtuelles

1. Dans la connexion Bastion de **myVM1**, ouvrez PowerShell.

2. Entrez `ping myvm2`.

    Vous recevez un message similaire à celui-ci :

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Fermez la connexion Bastion à **myVM1**.

4. Effectuez les étapes décrites dans [Se connecter à myVM1](#connect-to-myvm1), mais connectez-vous à **myVM2**.

5. Ouvrez PowerShell sur **myVM2**, puis entrez `ping myvm1`.

    Vous allez recevoir un message comme celui-ci :

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Fermez la connexion Bastion à **myVM2**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dans ce démarrage rapide, vous avez créé un réseau virtuel par défaut et deux machines virtuelles. 

Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez établi une communication sécurisée entre les deux machines virtuelles.

Lorsque vous avez fini d’utiliser le réseau virtuel et les machines virtuelles, supprimez le groupe de ressources et toutes les ressources qu’il contient :

1. Recherchez et sélectionnez **myResourceGroup**.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez **myResourceGroup** dans **TAPER LE NOM DU GROUPE DE RESSOURCES** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les paramètres de réseau virtuel, consultez [Créer, changer ou supprimer un réseau virtuel](manage-virtual-network.md).

Pour en savoir plus sur les types de communications réseau de machines virtuelles, consultez [Filtrer le trafic réseau](tutorial-filter-network-traffic.md).
