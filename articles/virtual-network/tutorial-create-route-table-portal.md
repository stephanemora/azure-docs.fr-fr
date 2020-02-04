---
title: Router le trafic réseau - Tutoriel - Portail Azure
titlesuffix: Azure Virtual Network
description: Dans ce didacticiel, découvrez comment acheminer le trafic réseau avec une table de routage à l’aide du portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2019
ms.author: kumud
ms.openlocfilehash: 96b6788e48b845ef7f0add11767eb36b47cac36b
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76775280"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutoriel : Acheminer le trafic réseau avec une table de routage à l’aide du portail Azure

Azure achemine le trafic entre tous les sous-réseaux au sein d’un réseau virtuel par défaut. Vous pouvez créer vos propres itinéraires pour remplacer le routage par défaut d’Azure. La possibilité de créer des itinéraires personnalisés est utile si, par exemple, vous souhaitez router le trafic entre des sous-réseaux via une appliance virtuelle réseau (NVA). Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une table de routage
> * Créer un itinéraire
> * Créer un réseau virtuel comprenant plusieurs sous-réseaux
> * Associer une table de routage à un sous-réseau
> * Créer une appliance NVA qui route le trafic
> * Déployer des machines virtuelles sur différents sous-réseaux
> * Router le trafic d’un sous-réseau vers un autre via une NVA

Si vous préférez, vous pouvez suivre ce didacticiel en utilisant [Azure CLI](tutorial-create-route-table-cli.md) ou [Azure PowerShell](tutorial-create-route-table-powershell.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-route-table"></a>Créer une table de routage

1. Dans le menu du Portail Azure, sélectionnez **Créer une ressource**.
2. Dans la zone de recherche, entrez *table de route*. Quand le terme **table de route** s’affiche dans les résultats de recherche, sélectionnez-le.
3. Dans la page **table de route**, sélectionnez **Créer**.
4. Dans **Créer une table de routage**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Name | Entrez *myRouteTablePublic*. |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **Créer**, entrez *myResourceGroup* et sélectionnez *OK*. |
    | Location | Sélectionnez **USA Est**.
    | Propagation de la route de la passerelle de réseau virtuel | Conservez la valeur par défaut **Activé**. |
5. Sélectionnez **Create** (Créer).

## <a name="create-a-route"></a>Créer un itinéraire

1. Dans la barre de recherche du portail, entrez *myRouteTablePublic*.

1. Quand **myRouteTablePublic** apparaît dans les résultats de la recherche, sélectionnez cette entrée.

1. Dans **myRouteTablePublic**, sous **Paramètres**, sélectionnez **Itinéraires** >  **+ Ajouter**.

    ![Ajouter un itinéraire](./media/tutorial-create-route-table-portal/add-route.png)

1. Dans **Ajouter un itinéraire**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de l’itinéraire | Entrez *ToPrivateSubnet*. |
    | Préfixe de l’adresse | Entrez *10.0.1.0/24*. |
    | Type de tronçon suivant | Sélectionnez **Appliance virtuelle**. |
    | adresse de tronçon suivant | Entrez *10.0.2.4*. |

1. Sélectionnez **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

Avant de pouvoir associer une table de routage à un sous-réseau, vous devez créer un réseau virtuel et un sous-réseau.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.

1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Name | Entrez *myVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.0.0.0/16*. |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez ***Sélectionner*** > **myResourceGroup**. |
    | Location | Conservez la valeur par défaut **USA Est**. |
    | Sous-réseau - Nom | Entrez *Public*. |
    | Plage d’adresses du sous-réseau | Entrez *10.0.0.0/24*. |

1. Conservez les autres valeurs par défaut et sélectionnez **Créer**.

### <a name="add-subnets-to-the-virtual-network"></a>Ajouter des sous-réseaux au réseau virtuel

1. Dans la barre de recherche du portail, entrez *myVirtualNetwork*.

1. Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.

1. Dans **myVirtualNetwork**, sous **Paramètres**, sélectionnez **Sous-réseaux** >  **+ Sous-réseau**.

    ![Ajouter un sous-réseau](./media/tutorial-create-route-table-portal/add-subnet.png)

1. Dans **Ajouter un sous-réseau**, entrez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Name | Entrez *Privé*. |
    | Espace d’adressage | Entrez *10.0.1.0/24*. |

1. Laissez les autres valeurs par défaut et sélectionnez **OK**.

1. Sélectionnez à nouveau **+ Sous-réseau**. Cette fois, entrez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Name | Entrez *DMZ*. |
    | Espace d’adressage | Entrez *10.0.2.0/24*. |

1. Comme précédemment, conservez les autres valeurs par défaut et sélectionnez **OK**.

    Azure affiche les trois sous-réseaux : **Public**, **Privé** et **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Associer myRouteTablePublic à votre sous-réseau Public

1. Sélectionnez **Public**.

1. Dans **Public**, sélectionnez **Table de routage** > **MyRouteTablePublic** > **Enregistrer**.

    ![Associer une table de routage](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Créer une NVA

Les NVA sont des machines virtuelles qui facilitent des fonctions réseau telles que le routage et l’optimisation du pare-feu. Si vous le souhaitez, vous pouvez sélectionner un autre système d’exploitation. Ce didacticiel suppose que vous utilisez **Windows Server 2016 Datacenter**.

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Calcul** > **Windows Server 2016 Datacenter**.

1. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVmNva*. |
    | Région | Sélectionnez **USA Est**. |
    | Options de disponibilité | Conservez la valeur par défaut **Aucune redondance d’infrastructure nécessaire**. |
    | Image | Conservez la valeur par défaut **Windows Server 2016 Datacenter**. |
    | Size | Conservez la valeur par défaut **Standard DS1 v2**. |
    | **COMPTE ADMINISTRATEUR** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **RÈGLES DES PORTS D’ENTRÉE** |  |
    | Aucun port d’entrée public | Sélectionnez **Aucun**.
    | **ÉCONOMISEZ DE L’ARGENT** |  |
    | Vous disposez déjà d’une licence Windows ? | Conservez la valeur par défaut **Non**. |

1. Sélectionnez **Suivant : Disques**.

1. Dans **Créer une machine virtuelle - Disques**, sélectionnez les paramètres répondant à vos besoins.

1. Sélectionnez **Suivant : Mise en réseau**.

1. Dans **Créer une machine virtuelle - Mise en réseau**, sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Réseau virtuel | Conservez la valeur par défaut **myVirtualNetwork**. |
    | Subnet | Sélectionnez **DMZ (10.0.2.0/24)** . |
    | Adresse IP publique | Sélectionnez **Aucun**. Vous n’avez pas besoin d’une adresse IP publique. La machine virtuelle ne se connectera pas à Internet.|

1. Conservez les autres valeurs par défaut et sélectionnez **Suivant : Gestion**.

1. Dans **Créer une machine virtuelle - Gestion**, pour **Compte de stockage de diagnostics**, sélectionnez **Créer**.

1. Dans **Créer un compte de stockage**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Name | Entrez *mynvastorageaccount*. |
    | Type de compte | Conservez la valeur par défaut **Stockage (usage général v1)** . |
    | Performances | Conservez la valeur par défaut **Standard**. |
    | Réplication | Conservez la valeur par défaut **Stockage localement redondant (LRS)** .

1. Sélectionnez **OK**.

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Revoir + créer** et Azure valide votre configuration.

1. Lorsque vous voyez **Validation réussie**, sélectionnez **Créer**.

    La création de la machine virtuelle ne nécessite que quelques minutes. Ne continuez pas tant qu’Azure n’a pas terminé la création de la machine virtuelle. La page **Votre déploiement est en cours** affiche les détails de déploiement.

1. Lorsque votre machine virtuelle est prête, sélectionnez **Accéder à la ressource**.

## <a name="turn-on-ip-forwarding"></a>Activer le transfert IP

Activez le transfert IP pour *myVmNva*. Lorsqu’Azure envoie le trafic réseau vers *myVmNva*, si le trafic est destiné à une autre adresse IP, le transfert IP enverra le trafic vers l’emplacement approprié.

1. Sur **myVmNva**, sous **Paramètres**, sélectionnez **Mise en réseau**.

1. Sélectionnez **myvmnva123**. Il s’agit de l’interface réseau créée par Azure pour votre machine virtuelle. Elle comportera une chaîne de nombres unique pour vous.

    ![Mise en réseau de machines virtuelles](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Sous **Paramètres**, sélectionnez **Configurations IP**.

1. Sur **myvmnva123 - Configurations IP**, pour **Transfert IP**, sélectionnez **Activé** , puis sélectionnez **Enregistrer**.

    ![Activer le transfert IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Créer des machines virtuelles publique et privée

Créez une machine virtuelle publique et une machine virtuelle privée dans le réseau virtuel. Vous les utiliserez plus tard pour voir si Azure achemine le trafic du sous-réseau *Public* vers le sous-réseau *Privé* via la NVA.

Suivez les étapes 1 à 12 de [Créer une NVA](#create-an-nva). Utilisez la plupart des mêmes paramètres. Ces valeurs sont celles qui doivent être différentes :

| Paramètre | Valeur |
| ------- | ----- |
| **MACHINE VIRTUELLE PUBLIQUE** | |
| PARAMÈTRES DE BASE |  |
| Nom de la machine virtuelle | Entrez *myVmPublic*. |
| MISE EN RÉSEAU | |
| Subnet | Sélectionnez **Public (10.0.0.0/24)** . |
| Adresse IP publique | Acceptez la valeur par défaut. |
| Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
| Sélectionner des ports d’entrée | Sélectionnez **HTTP** et **RDP**. |
| GESTION | |
| Compte de stockage de diagnostics | Conservez la valeur par défaut **mynvastorageaccount**. |
| **MACHINE VIRTUELLE PRIVÉE** | |
| PARAMÈTRES DE BASE |  |
| Nom de la machine virtuelle | Entrez *myVmPrivate*. |
| MISE EN RÉSEAU | |
| Subnet | Sélectionnez **Privé (10.0.1.0/24)** . |
| Adresse IP publique | Acceptez la valeur par défaut. |
| Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
| Sélectionner des ports d’entrée | Sélectionnez **HTTP** et **RDP**. |
| GESTION | |
| Compte de stockage de diagnostics | Conservez la valeur par défaut **mynvastorageaccount**. |

Vous pouvez créer la machine virtuelle *myVmPrivate* pendant qu’Azure crée la machine virtuelle *myVmPublic*. Ne passez pas à l’étape suivante avant qu’Azure ait terminé la création des deux machines virtuelles.

## <a name="route-traffic-through-an-nva"></a>Router le trafic via une NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Se connecter à myVmPrivate via le Bureau à distance

1. Dans la barre de recherche du portail, entrez *myVmPrivate*.

1. Quand la machine virtuelle **myVmPrivate** apparaît dans les résultats de la recherche, sélectionnez-la.

1. Sélectionnez **Se connecter** pour créer une connexion Bureau à distance à la machine virtuelle *myVmPrivate*.

1. Dans **Se connecter à la machine virtuelle**, sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( *.rdp*) et le télécharge sur votre ordinateur.

1. Ouvrez le fichier *.rdp* téléchargé.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle privée.

    1. Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte** pour utiliser les informations d’identification de la machine virtuelle privée.

1. Sélectionnez **OK**.

    Un avertissement de certificat peut s’afficher pendant le processus de connexion.

1. Sélectionnez **Oui** pour vous connecter à la machine virtuelle.

### <a name="enable-icmp-through-the-windows-firewall"></a>Activer le protocole ICMP via le pare-feu Windows

Dans une étape ultérieure, vous utiliserez l’outil de traçage d’itinéraire pour tester le routage. L’outil de traçage d’itinéraire utilise le protocole ICMP (Internet Control Message Protocol), que le pare-feu Windows refuse par défaut. Activez le protocole ICMP (Internet Control Message Protocol) via le pare-feu Windows.

1. Dans le Bureau à distance de *myVmPrivate*, ouvrez PowerShell.

1. Entrez cette commande :

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Vous utilisez le traçage d’itinéraire pour tester le routage dans ce didacticiel. Pour les environnements de production, nous ne recommandons pas d’autoriser le protocole ICMP (Internet Control Message Protocol) via le pare-feu Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Activer le transfert IP dans myVmNva

Vous avez [activé le transfert IP](#turn-on-ip-forwarding) pour l’interface réseau de la machine virtuelle à l’aide d’Azure. Le système d’exploitation de la machine virtuelle doit également transférer le trafic réseau. Activez le transfert IP pour le système d’exploitation de la machine virtuelle *myVmNva* avec ces commandes.

1. À partir d’une invite de commandes sur la machine virtuelle *myVmPrivate*, ouvrez un Bureau à distance à la machine virtuelle *myVmNva* :

    ```cmd
    mstsc /v:myvmnva
    ```

1. À partir de PowerShell sur *myVmNva*, entrez cette commande pour activer le transfert IP :

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Redémarrez la machine virtuelle *myVmNva*. Dans la barre des tâches, sélectionnez le **bouton Démarrer** > **bouton d’alimentation**, **Autres (Planifié)**  > **Continuer**.

    Ceci déconnecte également la session Bureau à distance.

1. Lorsque la machine virtuelle *myVmNva* a redémarré, créez une session Bureau à distance sur la machine virtuelle *myVmPublic*. Tout en conservant la connexion à la machine virtuelle *myVmPrivate*, ouvrez une invite de commandes et exécutez cette commande :

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Dans le Bureau à distance de *myVmPublic*, ouvrez PowerShell.

1. Activez le protocole ICMP (Internet Control Message Protocol) via le pare-feu Windows en entrant cette commande :

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Tester le routage du trafic réseau

Nous allons tout d’abord tester le routage du trafic réseau de la machine virtuelle *myVmPublic* vers la machine virtuelle *myVmPrivate*.

1. À partir de PowerShell, sur la machine virtuelle *myVmPublic*, entrez cette commande :

    ```powershell
    tracert myVmPrivate
    ```

    La réponse ressemble à cet exemple :

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Vous pouvez voir que le premier tronçon est dirigé vers 10.0.2.4. Il s’agit de l’adresse IP privée de la NVA. Le second tronçon est dirigé vers l’adresse IP privée de la machine virtuelle *myVmPrivate* : 10.0.1.4. Précédemment, vous avez ajouté l’itinéraire à la table de routage *myRouteTablePublic* et l’avez associé au sous-réseau *Public*. Azure a donc envoyé le trafic via la NVA et non directement au sous-réseau *Privé*.

1. Fermez la session Bureau à distance sur la machine virtuelle *myVmPublic*. Cela n’interrompt pas la connexion à la machine virtuelle *myVmPrivate*.

1. À partir d’une invite de commandes sur la machine virtuelle *myVmPrivate*, entrez cette commande :

    ```cmd
    tracert myVmPublic
    ```

    Elle teste le routage du trafic réseau de la machine virtuelle *myVmPrivate* vers la machine virtuelle *myVmPublic*. La réponse ressemble à cet exemple :

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Vous pouvez voir qu’Azure achemine le trafic directement de la machine virtuelle *myVmPrivate* vers la machine virtuelle *myVmPublic*. Par défaut, Azure achemine directement le trafic entre les sous-réseaux.

1. Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPrivate*.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources ainsi que toutes les ressources qu’il contient :

1. Dans la barre de recherche du portail, entrez *myResourceGroup*.

1. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une table de routage que vous avez associée à un sous-réseau. Vous avez créé une appliance virtuelle réseau (NVA) simple qui a routé le trafic d’un sous-réseau public vers un sous-réseau privé. Maintenant que vous savez comment le faire, vous pouvez déployer différentes NVA préconfigurées à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Elles proposent de nombreuses fonctions réseau qui vous seront utiles. Pour en savoir plus sur le routage, consultez [Routage du trafic de réseau virtuel](virtual-networks-udr-overview.md) et [Créer, modifier ou supprimer une table de routage](manage-route-table.md).

Alors que vous pouvez déployer de nombreuses ressources Azure dans un réseau virtuel, Azure ne peut pas déployer des ressources pour certains services PaaS dans un réseau virtuel. Il est possible de restreindre l’accès aux ressources de certains services Azure PaaS. La restriction doit uniquement utiliser le trafic d’un sous-réseau de réseau virtuel. Pour apprendre à restreindre l’accès réseau aux ressources Azure PaaS, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Restreindre l’accès réseau aux ressources PaaS](tutorial-restrict-network-access-to-resources.md)
