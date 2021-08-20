---
title: Restreindre l’accès réseau aux ressources PaaS - Tutoriel - Portail Azure
description: Dans ce tutoriel, découvrez comment limiter et restreindre l’accès réseau aux ressources Azure, comme le service Stockage Azure, à l’aide de points de terminaison de service de réseau virtuel en utilisant le portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 07/16/2021
ms.author: kumud
ms.openlocfilehash: a0d721e847d0e47358bfbeb640b9e9a6e6a551a3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114463361"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Tutoriel : Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service de réseau virtuel en utilisant le portail Azure

Les points de terminaison de service de réseau virtuel permettent de restreindre l’accès réseau à certaines ressources du service Azure en n’autorisant leur accès qu’à partir d’un sous-réseau du réseau virtuel. Vous pouvez également supprimer l’accès Internet aux ressources. Les points de terminaison de service fournissent une connexion directe entre votre réseau virtuel et les services Azure pris en charge, ce qui vous permet d’utiliser l’espace d’adressage privé de votre réseau virtuel pour accéder aux services Azure. Le trafic destiné aux ressources Azure via les points de terminaison de service reste toujours sur le serveur principal de Microsoft Azure. Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un réseau virtuel avec un sous-réseau
> * Ajouter un sous-réseau et activer un point de terminaison de service
> * Créer une ressource Azure et autoriser l’accès réseau à cette ressource uniquement à partir d’un sous-réseau
> * Déployer une machine virtuelle sur chaque sous-réseau
> * Vérifier l’accès à une ressource à partir d’un sous-réseau
> * Vérifier que l’accès à une ressource est refusé à partir d’un sous-réseau et d’Internet

Si vous préférez, vous pouvez suivre ce tutoriel en utilisant [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) ou [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure. Recherchez **Réseau virtuel**, puis sélectionnez **Créer**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-resources.png" alt-text="Capture d’écran de la recherche de réseau virtuel dans la page Créer une ressource.":::    

1. Dans l'onglet **De base**, entrez les informations suivantes, puis sélectionnez **Suivant : Adresses IP >** . 

   | Paramètre | Valeur |
   |----|----|
   | Abonnement | Sélectionnez votre abonnement|
   | Resource group | Sélectionnez **Créer** et entrez *myResourceGroup*.|
   | Nom | Entrez *myVirtualNetwork* |
   | Région | Sélectionnez **(États-Unis) USA Est** |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png" alt-text="Capture d’écran de l’onglet De base pour créer un réseau virtuel.":::  

1. Dans l'onglet **Adresses IP**, sélectionnez les paramètres d'adresse IP suivants, puis sélectionnez **Réviser + créer**.
   
   | Paramètre | Valeur |
   | --- | --- |
   | Espace d’adressage IPv4| Laissez la valeur par défaut. |
   | Nom du sous-réseau | Sélectionnez **Par défaut** et changez le nom du sous-réseau en « Public ». |
   | Plage d’adresses de sous-réseau | Laissez la valeur par défaut. |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-virtual-network-ip-addresses.png" alt-text="Capture d’écran de l’onglet Adresses IP pour créer un réseau virtuel.":::  

1. Si les contrôles de validation réussissent, sélectionnez **Créer**.

1. Attendez que le déploiement se termine, puis sélectionnez **Accéder à la ressource** ou passez à la section suivante. 

## <a name="enable-a-service-endpoint"></a>Activer un point de terminaison de service

Les points de terminaison de service sont activés par service, par sous-réseau. Pour créer un sous-réseau et activer un point de terminaison de service pour le sous-réseau :

1. Si vous n'êtes pas déjà sur la page de ressources du réseau virtuel, vous pouvez rechercher le réseau nouvellement créé dans la case située en haut du portail. Saisissez *myVirtualNetwork*, et sélectionnez-le dans la liste.

1. Sélectionnez **Sous-réseaux** sous *Paramètres*, puis sélectionnez **+ Sous-réseau** comme indiqué ici :

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-subnet.png" alt-text="Capture d’écran de l’ajout d’un sous-réseau à un réseau virtuel existant.":::

1. Dans la page **Ajouter un sous-réseau**, sélectionnez ou saisissez les informations suivantes, puis sélectionnez **Enregistrer** :

    | Paramètre |Valeur |
    | --- | --- |
    | Name | Privé |
    | Plage d’adresses de sous-réseau | Laissez la valeur par défaut|
    | Points de terminaison de service | Sélectionnez **Microsoft.Storage**|
    | Stratégies de points de terminaison de service | Conservez la valeur par défaut. *0 sélectionné*. |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-subnet-settings.png" alt-text="Capture d’écran de la page Ajouter un sous-réseau avec des points de terminaison de service configurés.":::  

> [!CAUTION]
> Avant d’activer un point de terminaison de service pour un sous-réseau existant qui contient des ressources, consultez [Modifier les paramètres de sous-réseau](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Restreindre l’accès réseau d’un sous-réseau

Par défaut, toutes les instances de machines virtuelles d’un sous-réseau peuvent communiquer avec l’ensemble des ressources. Vous pouvez limiter les communications vers et à partir de toutes les ressources d’un sous-réseau par la création d’un groupe de sécurité réseau et son association au sous-réseau :

1. Dans la zone de recherche située en haut du portail Azure, recherchez les **groupes de sécurité réseau**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/search-network-security-groups.png" alt-text="Capture d’écran de la recherche de groupes de sécurité réseau.":::  

1. Dans la page *Groupe de sécurité réseau*, sélectionnez **+ Créer**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/network-security-groups-page.png" alt-text="Capture d’écran de la page d’accueil des groupes de sécurité réseau."::: 

1. Entrez ou sélectionnez les informations suivantes :

    |Paramètre|Valeur|
    |----|----|
    |Abonnement| Sélectionnez votre abonnement|
    |Resource group | Sélectionnez *myResourceGroup* dans la liste|
    |Nom| Entrez **myNsgPrivate** |
    |Emplacement| Sélectionnez **USA Est**. |

1. Sélectionnez **Vérifier + créer** et, lorsque le contrôle de validation est réussi, sélectionnez **Créer**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-nsg-page.png" alt-text="Capture d’écran de la page Groupe de sécurité réseau.":::

1. Une fois le groupe de sécurité réseau créé, sélectionnez **Accéder à la ressource** ou recherchez *myNsgPrivate* en haut du portail Azure.

1. Sous **Paramètres**, sélectionnez *Règles de sécurité de trafic sortant*, puis sélectionnez **+ Ajouter**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-rule.png" alt-text="Capture d’écran de l’ajout d’une règle de sécurité sortante." lightbox="./media/tutorial-restrict-network-access-to-resources/create-outbound-rule-expanded.png":::

1. Créer une règle qui autorise les communications sortantes vers le service Stockage Azure. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Ajouter** :

    |Paramètre|Valeur|
    |----|----|
    |Source| Sélectionnez **VirtualNetwork** |
    |Source port ranges| * |
    |Destination | Sélectionnez **Service Tag** (Identification)|
    |Identification de destination | Sélectionnez **Stockage**|
    |Service | Laissez *Personnalisé* par défaut. |
    |Plages de ports de destination| Remplacez par *445*. Le protocole SMB est utilisé pour se connecter à un partage de fichiers créé lors d’une étape ultérieure. |
    |Protocol|Quelconque|
    |Action|Allow|
    |Priority|100|
    |Nom|Renommez avec **Allow-Storage-All**|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-storage-rule.png" alt-text="Capture d’écran de la création d’une sécurité sortante pour accéder au stockage.":::

1. Créer une règle de sécurité de trafic sortant qui refuse les communications vers Internet. Cette règle qui permet la communication Internet sortante se substitue à une règle par défaut dans tous les groupes de sécurité réseau. Répétez les étapes 6 à 9 ci-dessus en utilisant les valeurs suivantes, puis sélectionnez **Ajouter** :

    |Paramètre|Valeur|
    |----|----|
    |Source| Sélectionnez **VirtualNetwork** |
    |Source port ranges| * |
    |Destination | Sélectionnez **Service Tag** (Identification)|
    |Identification de destination| Sélectionnez **Internet**|
    |Service| Laissez *Personnalisé* par défaut. |
    |Plages de ports de destination| * |
    |Protocol|Quelconque|
    |Action| Modifiez la valeur par défaut en spécifiant **Refuser**. |
    |Priority|110|
    |Name|Remplacez par *Deny-Internet-All*|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-outbound-internet-rule.png" alt-text="Capture d’écran de la création d’une sécurité sortante pour bloquer l’accès internet.":::

1. Créez une *règle de sécurité de trafic entrant* qui autorise le trafic du protocole RDP (Remote Desktop Protocol) vers le sous-réseau en provenance de n’importe quel endroit. La règle remplace une règle de sécurité par défaut qui refuse tout le trafic entrant provenant d’Internet. Les connexions Bureau à distance sont autorisées sur le sous-réseau afin que la connectivité puisse être testée dans une étape ultérieure. Sous **Paramètres**, sélectionnez *Règles de sécurité de trafic entrant*, puis sélectionnez **+ Ajouter**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-inbound-rule.png" alt-text="Capture d’écran d’un ajout de règle de sécurité de trafic entrant." lightbox="./media/tutorial-restrict-network-access-to-resources/create-inbound-rule-expanded.png":::

1. Entrez ou sélectionnez les valeurs suivantes, puis sélectionnez **Ajouter**.

    |Paramètre|Valeur|
    |----|----|
    |Source| Quelconque |
    |Source port ranges| * |
    |Destination | Sélectionnez **VirtualNetwork**|
    |Plages de ports de destination| Remplacez par *3389* |
    |Protocol|Quelconque|
    |Action|Allow|
    |Priority|120|
    |Name|Remplacez par *Allow-RDP-All*|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-inbound-rdp-rule.png" alt-text="Capture d’écran de la création d’une règle autoriser le Bureau à distance entrant.":::

   >[!WARNING] 
   > Le port RDP 3389 est exposé à Internet. Ceci est recommandé uniquement pour les tests. Pour *Environnements de production*, nous vous recommandons d’utiliser une connexion VPN ou privée.

1.  Sélectionnez **Sous-réseaux** sous *Paramètres*, puis sélectionnez **+ Associer**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/associate-subnets-page.png" alt-text="Capture d’écran de la page d’association de sous-réseau des groupes de sécurité réseau.":::

1.  Sélectionnez **myVirtualNetwork** sous *Réseau virtuel*, puis sélectionnez **Privé** sous *Sous-réseaux*. Sélectionnez **OK** pour associer le groupe de sécurité réseau au sous-réseau sélectionné.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/associate-private-subnet.png" alt-text="Capture d’écran de l’association d’un groupe de sécurité réseau à un sous-réseau privé.":::

## <a name="restrict-network-access-to-a-resource"></a>Restreindre l’accès réseau à une ressource

Les étapes nécessaires pour restreindre l’accès réseau aux ressources créées par le biais des services Azure, qui sont activées pour les points de terminaison varient d’un service à l’autre. Pour connaître les étapes à suivre, consultez la documentation relative à chacun des services. La suite de ce tutoriel comprend des étapes permettant de restreindre, par exemple, l’accès réseau pour un compte Stockage Azure.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.

1. Entrez « Compte de stockage » dans la barre de recherche, puis sélectionnez-le dans le menu déroulant. Sélectionnez ensuite **Créer**.

1. Entrez les informations suivantes :

    |Paramètre|Valeur|
    |----|----|
    |Abonnement| Sélectionnez votre abonnement|
    |Resource group| Sélectionner *myResourceGroup*|
    |Nom du compte de stockage| Entrez un nom qui est unique dans tous les emplacements Azure. La longueur du nom doit être comprise entre 3 et 24 caractères, en utilisant uniquement des chiffres et des lettres minuscules.|
    |Région| Sélectionnez **(États-Unis) USA Est** |
    |Performances|standard|
    |Redondance| Stockage localement redondant (LRS)|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-storage-account.png" alt-text="Capture d'écran de la création d'un compte de stockage.":::

1. Sélectionnez **Créer + vérifier** et, lorsque les contrôles de validation ont réussi, cliquez sur **Créer**. 

    >[!NOTE] 
    > Le déploiement peut prendre quelques minutes.

1. Une fois le compte de stockage créé, sélectionnez **Accéder à la ressource**.

### <a name="create-a-file-share-in-the-storage-account"></a>Créer un partage de fichiers dans le compte de stockage

1. Sélectionnez **Partages de fichiers** sous *Stockage de données*, puis sélectionnez **+ Partage de fichiers**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/file-share-page.png" alt-text="Capture d’écran de la page de partage de fichiers dans un compte de stockage.":::

1. Entrez ou définissez les valeurs suivantes pour le partage de fichiers, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |----|----|
    |Nom| my-file-share|
    |Quota| Sélectionnez **Définir sur la valeur maximale**. |
    |Niveau| Laissez la valeur par défaut, *Transaction optimisée*. |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-new-file-share.png" alt-text="Capture d’écran de la page créer des paramètres de partage de fichiers.":::

1. Le nouveau partage de fichiers devrait apparaître sur la page de partage de fichiers, si ce n'est pas le cas, sélectionnez le bouton **Actualiser** en haut de la page.

### <a name="restrict-network-access-to-a-subnet"></a>Restreindre l’accès réseau à un sous-réseau

Par défaut, les comptes de stockage acceptent les connexions réseau provenant des clients de n’importe quel réseau, y compris Internet. Vous pouvez limiter l’accès réseau à partir d’Internet et de tous les autres sous-réseaux de tous les réseaux virtuels (à l’exception du sous-réseau *Privé* du réseau virtuel *myVirtualNetwork*). Pour limiter l’accès réseau à un sous-réseau :

1. Sous **Paramètres** pour votre compte de stockage (nom unique), sélectionnez *Mise en réseau*.

1. Sélectionnez *Autoriser l’accès de **Réseaux sélectionnés** _ puis _* + Ajouter un réseau existant**.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/storage-network-settings.png" alt-text="Capture d’écran de la page des paramètres de mise en réseau du compte de stockage.":::

1. Sous **Ajouter des réseaux**, sélectionnez les valeurs suivantes puis **Ajouter** :

    |Paramètre|Valeur|
    |----|----|
    |Abonnement| Sélectionnez votre abonnement|
    |Réseaux virtuels| **myVirtualNetwork**|
    |Sous-réseaux| **Privé**|

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/add-virtual-network.png" alt-text="Capture d’écran de la page d’ajout d’un réseau virtuel à un compte de stockage.":::

1. Sélectionnez le bouton **Enregistrer** pour enregistrer la configuration du réseau virtuel.

1. Sélectionnez **Clés d'accès** sous *Sécurité + mise en réseau* pour le compte de stockage et sélectionnez **Afficher les clés**. Notez la valeur de clé1 pour l'utiliser dans une étape ultérieure lors du mappage du partage de fichiers dans une machine virtuelle.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/storage-access-key.png" alt-text="Capture d’écran de la clé de compte de stockage et des chaînes de connexion." lightbox="./media/tutorial-restrict-network-access-to-resources/storage-access-key-expanded.png":::

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Pour tester l’accès réseau à un compte de stockage, déployez une machine virtuelle sur chaque sous-réseau.

### <a name="create-the-first-virtual-machine"></a>Créer la première machine virtuelle

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**.

1. Sélectionnez **Calcul**, puis **Créer** sous *Machine virtuelle*.

1. Sous l’onglet *Informations de base*, entrez ou sélectionnez les informations suivantes :

   |Paramètre|Valeur|
   |----|----|
   |Abonnement| Sélectionnez votre abonnement|
   |Resource group| Sélectionnez **myResourceGroup**, qui a été créé précédemment.|
   |Nom de la machine virtuelle| Entrez *myVmPublic*|
   |Région | (États-Unis) USA Est
   |Options de disponibilité| Zone de disponibilité|
   |Zone de disponibilité | 1 |
   |Image | Sélectionnez une image de système d’exploitation. Pour cette machine virtuelle, *Centre de données Windows Server 2019 – Gen1* est sélectionnée. |
   |Taille | Sélectionnez la taille d’instance de machine virtuelle que vous souhaitez utiliser |
   |Nom d’utilisateur|Entrez un nom d’utilisateur de votre choix.|
   |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.yml?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm-).|
   |Aucun port d’entrée public | Autoriser les ports sélectionnés |
   |Sélectionner des ports d’entrée | Conservez le paramètre par défaut *RDP (3389)* |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/create-public-vm-settings.png" alt-text="Capture d’écran de la création de paramètres de machine virtuelle publique." lightbox="./media/tutorial-restrict-network-access-to-resources/create-public-vm-settings-expanded.png":::
  
1. Sous l’onglet **Mise en réseau**, entrez ou sélectionnez les informations suivantes :

    |Paramètre|Valeur|
    |----|----|
    | Réseau virtuel | Sélectionnez **myVirtualNetwork**. |
    | Subnet | Sélectionnez **Public**. |
    | Groupe de sécurité réseau de la carte réseau | Sélectionnez **Avancé**. Le portail crée automatiquement pour vous un groupe de sécurité réseau qui autorise le port 3389. Vous aurez besoin de ce port ouvert pour vous connecter à la machine virtuelle dans une étape ultérieure. |

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/virtual-machine-networking.png" alt-text="Capture d’écran de la création de paramètres du réseau de machine virtuelle publique." lightbox="./media/tutorial-restrict-network-access-to-resources/virtual-machine-networking-expanded.png":::

1. Sélectionnez **Examiner et créer**, puis **Créer** et attendez la fin du déploiement.

1. Sélectionnez **Accéder à la ressource** ou ouvrez la page **Accueil > Machines virtuelles**, puis sélectionnez la machine virtuelle que vous venez de créer, *myVmPublic*, qui doit être démarrée.

### <a name="create-the-second-virtual-machine"></a>Créer la deuxième machine virtuelle

1. Répétez les étapes 1-5 pour créer une deuxième machine virtuelle. À l'étape 3, nommez la machine virtuelle *myVmPrivate* et définissez le *Goupe de sécurité réseau NIC* sur **Aucun**. À l’étape 4, sélectionnez le sous-réseau **Privé**.

   :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/virtual-machine-2-networking.png" alt-text="Capture d’écran de la création de paramètres du réseau de machine virtuelle privée." lightbox="./media/tutorial-restrict-network-access-to-resources/virtual-machine-2-networking-expanded.png":::

1. Sélectionnez **Examiner et créer**, puis **Créer** et attendez la fin du déploiement. 

    > [!WARNING]
    > Ne passez à l’étape suivante que lorsque le déploiement sera terminé.

1. Sélectionnez **Accéder à la ressource** ou ouvrez la page **Accueil > Machines virtuelles**, puis sélectionnez la machine virtuelle que vous venez de créer, *myVmPrivate*, qui doit être démarrée.

## <a name="confirm-access-to-storage-account"></a>Vérifier l’accès au compte de stockage

1. Une fois que la machine virtuelle *myVmPrivate* a été créée, allez à la page d'aperçu de la machine virtuelle. Pour vous connecter à la machine virtuelle, sélectionnez **Connexion**, puis sélectionnez **RDP** dans la liste déroulante.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/connect-private-vm.png" alt-text="Capture d’écran du bouton de connexion pour la machine virtuelle privée.":::

1. Sélectionnez **Télécharger le fichier RDP** pour télécharger le fichier de Bureau à distance sur votre ordinateur.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/download-rdp-file.png" alt-text="Capture d’écran de téléchargement du fichier RDP pour la machine virtuelle privée.":::
  
1. Ouvrez le fichier .rdp téléchargé. Lorsque vous y êtes invité, sélectionnez **Connexion**. 

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/rdp-connect.png" alt-text="Capture d’écran de la connexion pour la machine virtuelle privée.":::

1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vous devrez peut-être sélectionner **Plus de choix** puis **Utiliser un autre compte** pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle. Pour le champ d’adresse e-mail, entrez les informations d’identification « Compte d’administrateur : nom d’utilisateur » que vous avez spécifiées précédemment. Sélectionnez **OK** pour vous connecter à la machine virtuelle.

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/credential-screen.png" alt-text="Capture d’écran des informations de connexion pour la machine virtuelle privée.":::

    > [!NOTE] 
    > Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez **Oui** ou **Continuer** pour poursuivre le processus de connexion.

1. Une fois connecté, ouvrez Windows PowerShell. Utilisez le script ci-dessous pour mapper le partage de fichiers Azure au lecteur Z à l’aide de PowerShell. Remplacez `<storage-account-key>` et les deux variables `<storage-account-name>` par les valeurs récupérées plus tôt lors des étapes de [création du compte de stockage](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell retourne un résultat semblable à l’exemple suivant :

   ```powershell
   Name        Used (GB)     Free (GB) Provider      Root
   ----        ---------     --------- --------      ----
   Z                                      FileSystem    \\mystorage007.file.core.windows.net\my-f...
   ```

   Le partage de fichiers Azure est correctement mappé au lecteur Z.

1.   Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Vérifier que l’accès au compte de stockage est refusé

### <a name="from-myvmpublic"></a>À partir de myVmPublic :

1. Entrez *myVmPublic* dans le champ **Rechercher des ressources, services et documents** en haut du portail. Quand **myVmPublic** apparaît dans les résultats de la recherche, sélectionnez cette entrée.

1. Répétez les étapes 1 à 5 ci-dessus dans [Vérifier l’accès au compte de stockage](#confirm-access-to-storage-account) pour la machine virtuelle *myVmPublic*.

   Après un court délai d’attente, vous recevez une erreur `New-PSDrive : Access is denied`. L’accès est refusé car la machine virtuelle *myVmPublic* est déployée sur le sous-réseau *Public*. Le sous-réseau *Public* ne dispose d’aucun point de terminaison de service activé pour le Stockage Azure. Le compte de stockage permet uniquement l’accès à partir du sous-réseau *Private* et non au sous-réseau *Public*.

    ```powershell
    New-PSDrive : Access is denied
    At line:1 char:1
    + New-PSDrive -Name Z -PSProvider FileSystem -Root "\\mystorage007.file ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : InvalidOperation: (Z:PSDriveInfo) [New-PSDrive],     Win32Exception
        + Fu llyQualifiedErrorId : CouldNotMapNetworkDrive,Microsoft.PowerShell.Commands.NewPSDriveCommand

    ```

4. Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPublic*.

### <a name="from-a-local-machine"></a>À partir d’un ordinateur local :

1. Dans le portail Azure, accédez au compte de stockage nommé de manière unique que vous avez créé précédemment. Par exemple, *mystorage007*.

1. Sélectionnez **Partages de fichiers** sous *Stockage de données*, puis sélectionnez le fichier *my-file-share* créé plus tôt.

1. Vous devriez obtenir le message d’erreur suivant :

    :::image type="content" source="./media/tutorial-restrict-network-access-to-resources/access-denied-error.png" alt-text="Capture d’écran du message d’erreur Accès refusé.":::

>[!NOTE] 
> L’accès est refusé, car votre ordinateur ne se trouve pas dans le sous-réseau *Private* du réseau virtuel *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient :

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.

1. Sélectionnez **Supprimer le groupe de ressources**.

1. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez activé un point de terminaison de service pour un sous-réseau de réseau virtuel. Vous avez vu que les points de terminaison de service peuvent être activés pour les ressources déployées à partir de différents services Azure. Vous avez créé un compte Stockage Azure et un accès réseau à ce compte de stockage, limité aux ressources du sous-réseau du réseau virtuel. Pour en savoir plus sur les points de terminaison de service, consultez [Points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md) et [Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel](virtual-network-manage-subnet.md).

Si vous avez plusieurs réseaux virtuels dans votre compte, vous souhaiterez peut-être établir une connectivité entre eux afin que les ressources puissent communiquer entre elles. Pour savoir comment connecter des réseaux virtuels, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Connecter des réseaux virtuels](./tutorial-connect-virtual-networks-portal.md)
