---
title: Router le trafic réseau - Tutoriel - Portail Azure
titlesuffix: Azure Virtual Network
description: Dans ce didacticiel, découvrez comment acheminer le trafic réseau avec une table de routage à l’aide du portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: 7da59e996ec37d3653dbde68c5f56caa9e8261ee
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061908"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Tutoriel : Acheminer le trafic réseau avec une table de routage à l’aide du portail Azure

Azure achemine le trafic entre tous les sous-réseaux au sein d’un réseau virtuel par défaut. Vous pouvez créer vos propres itinéraires pour remplacer le routage par défaut d’Azure. Les itinéraires personnalisés sont utiles, par exemple, quand vous souhaitez router le trafic entre des sous-réseaux via une appliance virtuelle réseau (NVA). Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer une appliance NVA qui route le trafic
> * Créer une table de routage
> * Créer un itinéraire
> * Associer une table de routage à un sous-réseau
> * Déployer des machines virtuelles sur différents sous-réseaux
> * Router le trafic d’un sous-réseau vers un autre via une NVA

Ce tutoriel utilise le [portail Azure](https://portal.azure.com). Vous pouvez également utiliser [Azure CLI](tutorial-create-route-table-cli.md) ou [Azure PowerShell](tutorial-create-route-table-powershell.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous avez besoin d’un compte Azure doté d’un abonnement actif. Si vous n’en avez pas, vous pouvez [compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Dans le menu du Portail Azure, sélectionnez **Créer une ressource**. À partir de la Place de marché Azure, sélectionnez **Réseaux** > **Réseaux virtuels**, ou recherchez **Réseau virtuel** dans la zone de recherche.

2. Sélectionnez **Create** (Créer).

2. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Abonnement | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer** et entrez **myResourceGroup**. </br> Sélectionnez **OK**. |
    | Nom | Entrez **myVirtualNetwork**. |
    | Emplacement | Sélectionnez **(États-Unis) USA Est**.|

3. Sélectionnez l’onglet **Adresses IP**, ou sélectionnez le bouton **Suivant : Adresses IP** au bas de la page.

4. Dans **Espace d’adressage IPv4**, sélectionnez l’espace d’adressage existant, puis remplacez-le par **10.0.0.0/16**.

4. Sélectionnez **+ Ajouter un sous-réseau**, puis entrez **Public** comme **Nom du sous-réseau** et **10.0.0.0/24** comme **Plage d’adresses de sous-réseau**.

5. Sélectionnez **Ajouter**.

6. Sélectionnez **+ Ajouter un sous-réseau**, puis entrez **Privé** comme **Nom du sous-réseau** et **10.0.1.0/24** comme **Plage d’adresses de sous-réseau**.

7. Sélectionnez **Ajouter**.

8. Sélectionnez **+ Ajouter un sous-réseau**, puis entrez **DMZ** comme **Nom du sous-réseau** et **10.0.2.0/24** comme **Plage d’adresses de sous-réseau**.

9. Sélectionnez **Ajouter**.

10. Sélectionnez l’onglet **Sécurité**, ou sélectionnez le bouton **Suivant : Sécurité** situé au bas de la page.

11. Sous **BastionHost**, sélectionnez **Activer**. Entrez les informations suivantes :

    | Paramètre            | Valeur                      |
    |--------------------|----------------------------|
    | Nom du bastion | Entrez **myBastionHost** |
    | Espace d’adressage AzureBastionSubnet | Entrez **10.0.3.0/24** |
    | Adresse IP publique | Sélectionnez **Créer nouveau**. </br> Pour **Nom**, entrez **myBastionIP**. </br> Sélectionnez **OK**. |

12. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton **Vérifier + créer**.

13. Sélectionnez **Create** (Créer).

## <a name="create-an-nva"></a>Créer une NVA

Les appliances virtuelles réseau (NVA) sont des machines virtuelles qui facilitent les fonctions réseau, telles que le routage et l’optimisation du pare-feu. Ce tutoriel suppose que vous utilisez **Windows Server 2019 Datacenter**. Si vous le souhaitez, vous pouvez sélectionner un autre système d’exploitation.

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**. 
   
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionner **myResourceGroup** |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVMNVA** |
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
    | Réseau virtuel | Sélectionnez **myVirtualNetwork**. |
    | Subnet | Sélectionnez **DMZ** |
    | Adresse IP publique | Sélectionnez **Aucun** |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **De base**|
    | Réseau des ports entrants publics | Sélectionnez **Aucun**. |
   
5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

## <a name="create-a-route-table"></a>Créer une table de routage

1. Dans le menu du [Portail Azure](https://portal.azure.com) ou dans la page **Accueil**, sélectionnez **Créer une ressource**.

2. Dans la zone de recherche, entrez **table de route**. Quand le terme **table de route** s’affiche dans les résultats de recherche, sélectionnez-le.

3. Dans la page **table de route**, sélectionnez **Créer**.

4. Dans **Créer une table de route**, sous l’onglet **Informations de base**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **Détails du projet** |   |
    | Abonnement | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **myResourceGroup**. |
    | **Détails de l’instance** |    |
    | Région | Sélectionnez **USA Est**. |
    | Name | Entrez **myRouteTablePublic**. |
    | Propager des itinéraires de passerelle | Sélectionnez **Oui**. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Créer une table de route - Portail Azure." border="true":::

5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.

## <a name="create-a-route"></a>Créer un itinéraire

1. Accédez au [portail Azure](https://portal.azure.com) pour gérer votre table de route. Recherchez et sélectionnez **Tables d’itinéraires**.

2. Sélectionnez le nom de votre table de route **myRouteTablePublic**.

3. Dans la page **myRouteTablePublic**, dans la section **Paramètres**, sélectionnez **Routes**.

4. Dans la page Routes, sélectionnez le bouton **+ Ajouter**.

5. Dans **Ajouter un itinéraire**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom de l’itinéraire | Entrez **ToPrivateSubnet** |
    | Préfixe de l’adresse | Entrez **10.0.1.0/24** (plage d’adresses du sous-réseau **Privé** créé précédemment) |
    | Type de tronçon suivant | Sélectionnez **Appliance virtuelle**. |
    | adresse de tronçon suivant | Entrez **10.0.2.4** (adresse dans la plage d’adresses du sous-réseau **DMZ**) |

6. Sélectionnez **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Associer une table de routage à un sous-réseau

1. Accédez au [portail Azure](https://portal.azure.com) pour gérer votre réseau virtuel. Recherchez et sélectionnez **Réseaux virtuels**.

2. Sélectionnez le nom de votre réseau virtuel **myVirtualNetwork**.

3. Dans la page **myVirtualNetwork**, dans la section **Paramètres**, sélectionnez **Sous-réseaux**.

4. Dans la liste de sous-réseaux du réseau virtuel, sélectionnez **Public**.

5. Dans **Table de route**, choisissez la table de route que vous avez créée **myRouteTablePublic**. 

6. Sélectionnez **Enregistrer** pour associer votre table de route au sous-réseau **Public**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Associer une table de route, liste de sous-réseaux, réseau virtuel, portail Azure." border="true":::

## <a name="turn-on-ip-forwarding"></a>Activer le transfert IP

Ensuite, activez le transfert IP pour votre nouvelle machine virtuelle NVA, **myVMNVA**. Quand Azure envoie le trafic réseau vers **myVMNVA**, si le trafic est destiné à une autre adresse IP, le transfert IP envoie le trafic vers l’emplacement approprié.

1. Accédez au [portail Azure](https://portal.azure.com) pour gérer votre machine virtuelle. Recherchez et sélectionnez **Machines virtuelles**.

2. Sélectionnez le nom de votre machine virtuelle **myVMNVA**.

3. Dans la page de vue d’ensemble de **myVMNVA**, sous **Paramètres**, sélectionnez **Réseaux**.

4. Dans la page **Réseaux** de **myVMNVA**, sélectionnez l’interface réseau en regard d’**Interface réseau**.  Le nom de l’interface commence par **myvmnva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Réseau, machine virtuelle NVA (appliance virtuelle réseau), portail Azure" border="true":::

5. Dans la page de vue d’ensemble de l’interface réseau, sous **Paramètres**, sélectionnez **Configurations IP**.

6. Dans la page **Configurations IP**, définissez **Transfert IP** sur **Activé**, puis sélectionnez **Enregistrer**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="Activer le transfert IP" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Créer des machines virtuelles publique et privée

Créez une machine virtuelle publique et une machine virtuelle privée dans le réseau virtuel. Vous les utiliserez plus tard pour voir si Azure achemine le trafic du sous-réseau **Public** vers le sous-réseau **Privé** via la NVA.


### <a name="public-vm"></a>Machine virtuelle publique

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**. 
   
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionner **myResourceGroup** |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVMPublic** |
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
    | Réseau virtuel | Sélectionnez **myVirtualNetwork**. |
    | Subnet | Sélectionnez **Public** |
    | Adresse IP publique | Sélectionnez **Aucun** |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **De base**|
    | Réseau des ports entrants publics | Sélectionnez **Aucun**. |
   
5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

### <a name="private-vm"></a>Machine virtuelle privée

1. En haut à gauche du portail, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**. 
   
2. Dans **Créer une machine virtuelle**, tapez ou sélectionnez les valeurs sous l’onglet **De base** :

    | Paramètre | Valeur                                          |
    |-----------------------|----------------------------------|
    | **Détails du projet** |  |
    | Abonnement | Sélectionner votre abonnement Azure |
    | Groupe de ressources | Sélectionner **myResourceGroup** |
    | **Détails de l’instance** |  |
    | Nom de la machine virtuelle | Entrez **myVMPrivate** |
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
    | Réseau virtuel | Sélectionnez **myVirtualNetwork**. |
    | Subnet | Sélectionnez **Privé** |
    | Adresse IP publique | Sélectionnez **Aucun** |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **De base**|
    | Réseau des ports entrants publics | Sélectionnez **Aucun**. |
   
5. Sélectionnez l’onglet **Vérifier + créer**, ou sélectionnez le bouton bleu **Vérifier + créer** situé au bas de la page.
  
6. Passez en revue les paramètres, puis sélectionnez **Créer**.

## <a name="route-traffic-through-an-nva"></a>Router le trafic via une NVA

### <a name="sign-in-to-private-vm"></a>Se connecter à une machine virtuelle privée

1. Accédez au [portail Azure](https://portal.azure.com) pour gérer votre machine virtuelle privée. Recherchez et sélectionnez **Machines virtuelles**.

2. Choisissez le nom de votre machine virtuelle privée **myVMPrivate**.

3. Dans la barre de menus de la machine virtuelle, sélectionnez **Se connecter**, puis sélectionner **Bastion**.

4. Dans la page **Se connecter**, sélectionnez le bouton bleu **Utiliser Bastion**.

5. Dans la page **Bastion**, entrez le nom d’utilisateur et le mot de passe que vous avez créés pour la machine virtuelle.

6. Sélectionnez **Connecter**.

### <a name="configure-firewall"></a>Configurer le pare-feu

Dans une étape ultérieure, vous utiliserez l’outil de traçage d’itinéraire pour tester le routage. L’outil de traçage d’itinéraire utilise le protocole ICMP (Internet Control Message Protocol), que le pare-feu Windows refuse par défaut. 

Activez le protocole ICMP (Internet Control Message Protocol) via le pare-feu Windows.

1. Dans la connexion Bastion de **myVMPrivate**, ouvrez PowerShell avec des privilèges d’administrateurs.

2. Entrez cette commande :

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Vous allez utiliser le traçage d’itinéraire pour tester le routage dans ce tutoriel. Pour les environnements de production, nous ne recommandons pas d’autoriser le protocole ICMP (Internet Control Message Protocol) via le pare-feu Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Activer le transfert IP dans myVMNVA

Vous avez [activé le transfert IP](#turn-on-ip-forwarding) pour l’interface réseau de la machine virtuelle à l’aide d’Azure. Le système d’exploitation de la machine virtuelle doit également transférer le trafic réseau. 

Activez le transfert IP pour **myVMNVA** avec ces commandes.

1. À partir de PowerShell sur la machine virtuelle **myVMPrivate**, ouvrez un bureau à distance sur la machine virtuelle **myVMNVA** :

    ```powershell
    mstsc /v:myvmnva
    ```

2. À partir de PowerShell sur la machine virtuelle **myVMNVA**, entrez cette commande pour activer le transfert IP :

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Redémarrez **myVMNVA**.

    ```powershell
    Restart-Computer
    ```

4. Après le redémarrage de **myVMNVA**, créez une session Bureau à distance sur **myVMPublic**. 
    
    Tout restant connecté à **myVMPrivate**, ouvrez PowerShell, puis exécutez cette commande :

    ```powershell
    mstsc /v:myvmpublic
    ```
5. Dans le Bureau à distance de **myVMPublic**, ouvrez PowerShell.

6. Activez le protocole ICMP (Internet Control Message Protocol) via le pare-feu Windows en entrant cette commande :

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Tester le routage du trafic réseau

Commençons par tester le routage du trafic réseau de **myVMPublic** vers **myVMPrivate**.

1. À partir de PowerShell, sur **myVMPublic**, entrez cette commande :

    ```powershell
    tracert myvmprivate
    ```

    La réponse ressemble à cet exemple :

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * Vous pouvez voir que le premier tronçon est dirigé vers 10.0.2.4, ce qui correspond à l’adresse IP privée de **myVMNVA**. 

    * Le second tronçon est dirigé vers l’adresse IP privée de **myVMPrivate** : 10.0.1.4. 

    Précédemment, vous avez ajouté l’itinéraire à la table de routage **myRouteTablePublic** et l’avez associé au sous-réseau *Public*. Azure a envoyé le trafic via l’appliance virtuelle réseau (NVA) et non directement au sous-réseau *Privé*.

2. Fermez la session Bureau à distance sur **myVMPublic**. Cela n’interrompt pas la connexion à **myVMPrivate**.

3. Ouvrez PowerShell sur **myVMPrivate**, puis entrez la commande suivante :

    ```powershell
    tracert myvmpublic
    ```

    Cette commande teste le routage du trafic réseau de la machine virtuelle *myVmPrivate* vers la machine virtuelle *myVmPublic*. La réponse ressemble à cet exemple :

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    Vous pouvez voir qu’Azure route le trafic directement de *myVMPrivate* vers *myVMPublic*. Par défaut, Azure achemine directement le trafic entre les sous-réseaux.

4. Fermez la session Bastion sur **myVMPrivate**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez **myResourceGroup** et toutes les ressources qu’il contient :

1. Accédez au [portail Azure](https://portal.azure.com) pour gérer votre groupe de ressources. Recherchez et sélectionnez **Groupes de ressources**.

2. Sélectionnez le nom de votre groupe de ressources **myResourceGroup**.

3. Sélectionnez **Supprimer le groupe de ressources**.

4. Dans la boîte de dialogue de confirmation, entrez **myResourceGroup** pour **TAPEZ LE NOM DU GROUPE DE RESSOURCES**, puis sélectionnez **Supprimer**. 


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

* Créé une table de route que vous avez associée à un sous-réseau.
* Créé une appliance virtuelle réseau (NVA) simple qui a routé le trafic d’un sous-réseau public vers un sous-réseau privé. 

Vous pouvez déployer, à partir de la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), différentes appliances virtuelles réseau préconfigurées qui fournissent de nombreuses fonctions réseau utiles. 

Pour en savoir plus sur le routage, consultez [Routage du trafic de réseau virtuel](virtual-networks-udr-overview.md) et [Créer, modifier ou supprimer une table de routage](manage-route-table.md).

Pour filtrer le trafic réseau dans un réseau virtuel, consultez :
> [!div class="nextstepaction"]
> [Tutoriel : Filtrer le trafic réseau avec un groupe de sécurité réseau à l’aide du portail Azure](tutorial-filter-network-traffic.md)
