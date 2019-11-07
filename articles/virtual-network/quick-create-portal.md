---
title: Créer un réseau virtuel - Démarrage rapide - Portail Azure
titlesuffix: Azure Virtual Network
description: Dans ce démarrage rapide, vous découvrez comment créer un réseau virtuel à l’aide du portail Azure. Un réseau virtuel permet à des ressources Azure telles que des machines virtuelles de communiquer en toute sécurité entre elles et avec Internet.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to create a virtual network so that virtual machines can securely communicate with each other and with the internet.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/08/2019
ms.author: kumud
ms.openlocfilehash: d8e95f9c345a943eb458800b852640e3f1fde907
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488503"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Démarrage rapide : Créer un réseau virtuel au moyen du portail Azure

Un réseau virtuel est le bloc de construction fondamental de votre réseau privé dans Azure. Il permet à des ressources Azure telles que des machines virtuelles de communiquer en toute sécurité entre elles et avec Internet. Ce guide de démarrage rapide explique comment créer un réseau virtuel à l’aide du portail Azure. Ensuite, vous pouvez déployer deux machines virtuelles dans le réseau virtuel, communiquer en toute sécurité entre machines virtuelles et vous connecter aux machines virtuelles à partir d’Internet.


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [Portail Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Dans le menu du Portail Azure, sélectionnez **Créer une ressource**.

2. À partir de la Place de marché Azure, sélectionnez **Mise en réseau** > **Réseau virtuel**.

3. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez *myVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.1.0.0/16*. |
    | Subscription | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Location | Sélectionnez **USA Est**.|
    | Nom du sous-réseau | Entrez *myVirtualSubnet*. |
    | Plage d’adresses du sous-réseau | Entrez *10.1.0.0/24*. |

4. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez deux machines virtuelles dans le réseau virtuel :

### <a name="create-the-first-vm"></a>Créer la première machine virtuelle

1. Dans le menu du Portail Azure, sélectionnez **Créer une ressource**.

2. À partir de la Place de marché Azure, sélectionnez **Calcul** > **Windows Server 2019 Datacenter**.

3. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente. |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVm1*. |
    | Région | Sélectionnez **USA Est**. |
    | Options de disponibilité | Conservez la valeur par défaut **Aucune redondance d’infrastructure nécessaire**. |
    | Image | Conservez la valeur par défaut **Windows Server 2019 Datacenter**. |
    | Size | Conservez la valeur par défaut **Standard DS1 v2**. |
    | **COMPTE ADMINISTRATEUR** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **RÈGLES DES PORTS D’ENTRÉE** |  |
    | Aucun port d’entrée public | Conservez la valeur par défaut **Aucun**. |
    | **ÉCONOMISEZ DE L’ARGENT** |  |
    | Vous disposez déjà d’une licence Windows ? | Conservez la valeur par défaut **Non**. |

4. Sélectionnez **Suivant : Disks**.

5. Dans **Créer une machine virtuelle - Disks**, conservez les valeurs par défaut et sélectionnez **Suivant : Mise en réseau**.

6. Dans **Créer une machine virtuelle - Mise en réseau**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Réseau virtuel | Conservez la valeur par défaut **myVirtualNetwork**. |
    | Subnet | Conservez la valeur par défaut **myVirtualSubnet (10.1.0.0/24)** . |
    | Adresse IP publique | Conservez la valeur par défaut **(new) myVm-ip**. |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **HTTP** et **RDP**.

7. Sélectionnez **Suivant : Gestion**.

8. Dans **Créer une machine virtuelle - Gestion**, pour **Compte de stockage de diagnostics**, sélectionnez **Créer**.

9. Dans **Créer un compte de stockage**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez *myvmstorageaccount*. Si ce nom est utilisé, créez un nom unique.|
    | Type de compte | Conservez la valeur par défaut **Stockage (usage général v1)** . |
    | Performances | Conservez la valeur par défaut **Standard**. |
    | Réplication | Conservez la valeur par défaut **Stockage localement redondant (LRS)** . |

10. Sélectionnez **OK**.

11. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration.

12. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

### <a name="create-the-second-vm"></a>Créer la seconde machine virtuelle

1. Effectuez les étapes 1 et 9 ci-dessus.

    > [!NOTE]
    > À l’étape 2, pour **Nom de la machine virtuelle**, entrez *myVm2*.
    >
    > À l’étape 7, pour **Compte de stockage de diagnostic**, veillez à sélectionner **myvmstorageaccount**.

2. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Revoir + créer** et Azure valide votre configuration.

3. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Après avoir créé *myVm1*, connecter-vous à Internet.

1. Dans la barre de recherche du portail, entrez *myVm1*.

2. Sélectionnez le bouton **Connexion**.

    ![Connexion à une machine virtuelle](./media/quick-create-portal/connect-to-virtual-machine.png)

    Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

3. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier *.rdp* (Remote Desktop Protocol) et le télécharge sur votre ordinateur.

4. Ouvrez le fichier *.rdp* téléchargé.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    2. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

        > [!NOTE]
        > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

5. Sélectionnez **OK**.

6. Vous recevrez peut-être un avertissement de certificat lors du processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

7. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.

## <a name="communicate-between-vms"></a>Établir une communication entre les machines virtuelles

1. Dans le Bureau à distance de *myVm1*, ouvrez PowerShell.

2. Entrez `ping myVm2`.

    Vous recevez un message similaire à celui ci :

    ```powershell
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    `ping` a échoué car `ping` utilise le protocole ICMP (Internet Control Message Protocol). Par défaut, le protocole ICMP n’est pas autorisé par le pare-feu Windows.

3. Pour autoriser *myVm2* à effectuer un test ping *myVm1* entrez la commande suivante dans une étape ultérieure :

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Cette commande autorise le protocole ICMP entrant via le pare-feu Windows :

4. Fermez la connexion Bureau à distance sur *myVm1*.

5. Effectuez à nouveau les étapes dans [Se connecter à une machine virtuelle à partir d’internet](#connect-to-a-vm-from-the-internet), mais connectez-vous à *myVm2*.

6. À partir d’une invite de commandes, entrez `ping myvm1`.

    Vous allez obtenir un message comme celui-ci :

    ```powershell
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Vous recevez des réponses de *myVm1*, car vous avez autorisé ICMP via le pare-feu Windows sur la machine virtuelle *myVm1* à l’étape 3.

7. Fermez la connexion Bureau à distance sur *myVm2*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous avez fini d’utiliser le réseau virtuel et les machines virtuelles, supprimez le groupe de ressources et toutes les ressources qu’il contient :

1. Entrez *myResourceGroup* dans la zone **Recherche** en haut du portail, puis sélectionnez **myResourceGroup** dans les résultats de la recherche.

2. Sélectionnez **Supprimer le groupe de ressources**.

3. Entrez *myResourceGroup* dans **TAPER LE NOM DU GROUPE DE RESSOURCES** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un réseau virtuel par défaut et deux machines virtuelles. Vous vous êtes connecté à une machine virtuelle à partir d’Internet et avez établi une communication sécurisée entre les deux machines virtuelles. Pour plus d’informations sur les paramètres des réseaux virtuels, consultez [Gérer un réseau virtuel](manage-virtual-network.md).

Par défaut, Azure autorise une communication sécurisée illimitée entre des machines virtuelles. À l’inverse, il permet uniquement les connexions de Bureau à distance entrantes pour les machines virtuelles Windows depuis Internet. Pour en savoir plus sur la configuration des différents types de communications de réseau de machine virtuelle, accédez au didacticiel [Filtrer le trafic réseau](tutorial-filter-network-traffic.md).
