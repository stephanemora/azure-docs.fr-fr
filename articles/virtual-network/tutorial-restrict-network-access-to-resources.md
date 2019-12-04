---
title: Restreindre l’accès réseau aux ressources PaaS - Tutoriel - Portail Azure
description: Dans ce tutoriel, découvrez comment limiter et restreindre l’accès réseau aux ressources Azure, telles que le service Stockage Azure et Azure SQL Database, à l’aide de points de terminaison de service de réseau virtuel en utilisant le portail Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/23/2018
ms.author: kumud
ms.openlocfilehash: 85fc5687b82947ed16bde0c30ca2b947514ba958
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186368"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Didacticiel : Restreindre l’accès réseau aux ressources PaaS avec des points de terminaison de service de réseau virtuel en utilisant le portail Azure

Les points de terminaison de service de réseau virtuel permettent de restreindre l’accès réseau à certaines ressources du service Azure en n’autorisant leur accès qu’à partir d’un sous-réseau du réseau virtuel. Vous pouvez également supprimer l’accès Internet aux ressources. Les points de terminaison de service fournissent une connexion directe entre votre réseau virtuel et les services Azure pris en charge, ce qui vous permet d’utiliser l’espace d’adressage privé de votre réseau virtuel pour accéder aux services Azure. Le trafic destiné aux ressources Azure via les points de terminaison de service reste toujours sur le serveur principal de Microsoft Azure. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel avec un sous-réseau
> * Ajouter un sous-réseau et activer un point de terminaison de service
> * Créer une ressource Azure et autoriser l’accès réseau à cette ressource uniquement à partir d’un sous-réseau
> * Déployer une machine virtuelle sur chaque sous-réseau
> * Vérifier l’accès à une ressource à partir d’un sous-réseau
> * Vérifier que l’accès à une ressource est refusé à partir d’un sous-réseau et d’Internet

Si vous préférez, vous pouvez suivre ce tutoriel en utilisant [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) ou [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Réseau virtuel**.
3. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Créer** :

   |Paramètre|Valeur|
   |----|----|
   |Nom| myVirtualNetwork |
   |Espace d’adressage| 10.0.0.0/16|
   |Subscription| Sélectionnez votre abonnement|
   |Resource group | Sélectionnez **Créer** et entrez *myResourceGroup*.|
   |Location| Sélectionnez **USA Est**. |
   |Nom du sous-réseau| Public|
   |Plage d’adresses de sous-réseau| 10.0.0.0/24|
   |Protection DDoS| De base|
   |Points de terminaison de service| Désactivé|
   |Pare-feu| Désactivé|

   ![Entrer des informations de base sur votre réseau virtuel](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

## <a name="enable-a-service-endpoint"></a>Activer un point de terminaison de service

Les points de terminaison de service sont activés par service, par sous-réseau. Créer un sous-réseau et activer un point de terminaison de service pour le sous-réseau.

1. Dans le champ **Rechercher des ressources, services et documents** en haut du portail, entrez *myVirtualNetwork.* Quand la mention **myVirtualNetwork** apparaît dans les résultats de recherche, sélectionnez-la.
2. Ajoutez un sous-réseau au réseau virtuel. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**, puis **+ Sous-réseau**, comme montré dans l’image suivante :

    ![Ajouter un sous-réseau](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Sous **Ajouter un sous-réseau**, sélectionnez ou saisissez les informations suivantes, puis sélectionnez **OK** :

    |Paramètre|Valeur|
    |----|----|
    |Nom| Privé |
    |Plage d’adresses| 10.0.1.0/24|
    |Points de terminaison de service| Sélectionnez **Microsoft.Storage** sous **Services**|

> [!CAUTION]
> Avant d’activer un point de terminaison de service pour un sous-réseau existant qui contient des ressources, consultez [Modifier les paramètres de sous-réseau](virtual-network-manage-subnet.md#change-subnet-settings).

## <a name="restrict-network-access-for-a-subnet"></a>Restreindre l’accès réseau d’un sous-réseau

Par défaut, toutes les machines virtuelles d’un sous-réseau peuvent communiquer avec l’ensemble des ressources. Vous pouvez limiter les communications vers et à partir de toutes les ressources d’un sous-réseau par la création d’un groupe de sécurité réseau et l’association au sous-réseau.

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Mise en réseau**, puis **Groupe de sécurité réseau**.
3. Sous **Créer un groupe de sécurité réseau**, saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |----|----|
    |Nom| myNsgPrivate |
    |Subscription| Sélectionnez votre abonnement|
    |Resource group | Sélectionnez **Utiliser l’existant**, puis *myResourceGroup*.|
    |Location| Sélectionnez **USA Est**. |

4. Une fois le groupe de sécurité réseau créé, entrez *myNsgPrivate*, dans le champ **Rechercher des ressources, services et documents** en haut du portail. Quand **myNsgPrivate** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
5. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic sortant**.
6. Sélectionnez **Ajouter**.
7. Créer une règle qui autorise les communications sortantes vers le service Stockage Azure. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **Ajouter** :

    |Paramètre|Valeur|
    |----|----|
    |Source| Sélectionnez **VirtualNetwork** |
    |Source port ranges| * |
    |Destination | Sélectionnez **Service Tag** (Identification)|
    |Identification de destination | Sélectionnez **Stockage**|
    |Plages de ports de destination| * |
    |Protocol|Quelconque|
    |Action|AUTORISER|
    |Priority|100|
    |Nom|Allow-Storage-All|

8. Créer une règle de sécurité de trafic sortant qui refuse les communications vers Internet. Cette règle qui permet la communication Internet sortante se substitue à une règle par défaut dans tous les groupes de sécurité réseau. Répétez les étapes 5 à 7 en utilisant les valeurs suivantes :

    |Paramètre|Valeur|
    |----|----|
    |Source| Sélectionnez **VirtualNetwork** |
    |Source port ranges| * |
    |Destination | Sélectionnez **Service Tag** (Identification)|
    |Identification de destination| Sélectionnez **Internet**|
    |Plages de ports de destination| * |
    |Protocol|Quelconque|
    |Action|Deny|
    |Priority|110|
    |Nom|Deny-Internet-All|

9. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic entrant**.
10. Sélectionnez **Ajouter**.
11. Créez une règle de sécurité de trafic entrant qui autorise le trafic du protocole RDP (Remote Desktop Protocol) vers le sous-réseau en provenance de n’importe quel endroit. La règle remplace une règle de sécurité par défaut qui refuse tout le trafic entrant provenant d’Internet. Les connexions Bureau à distance sont autorisées sur le sous-réseau afin que la connectivité puisse être testée dans une étape ultérieure. Sous **PARAMÈTRES**, sélectionnez **Règles de sécurité de trafic entrant**, puis **+Ajouter**, entrez les valeurs suivantes et sélectionnez **Ajouter** :

    |Paramètre|Valeur|
    |----|----|
    |Source| Quelconque |
    |Source port ranges| * |
    |Destination | Sélectionnez **VirtualNetwork**|
    |Plages de ports de destination| 3389 |
    |Protocol|Quelconque|
    |Action|AUTORISER|
    |Priority|120|
    |Nom|Allow-RDP-All|

12. Sous **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
13. Sélectionnez **+ Associer**
14. Sous **Associer un sous-réseau**, sélectionnez **Réseau virtuel** puis **myVirtualNetwork** sous **Choisir un réseau virtuel**.
15. Sous **Sous-réseau**, sélectionnez **Private** puis **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Restreindre l’accès réseau à une ressource

Les étapes nécessaires pour restreindre l’accès réseau aux ressources créées par le biais des services Azure avec activation des points de terminaison varient d’un service à l’autre. Pour connaître les étapes à suivre, consultez la documentation relative à chacun des services. La suite de ce didacticiel comprend des étapes permettant de restreindre, par exemple, l’accès réseau pour un compte Stockage Azure.

### <a name="create-a-storage-account"></a>Créez un compte de stockage.

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Stockage**, puis **Compte de stockage - blob, fichier, table, file d’attente**.
3. Saisissez ou sélectionnez les informations suivantes, acceptez les autres valeurs par défaut, puis sélectionnez **Créer** :

    |Paramètre|Valeur|
    |----|----|
    |Nom| Entrez un nom qui n’existe dans aucun autre emplacement Azure. Le nom doit comprendre entre 3 et 24 caractères, correspondant à des chiffres et à des lettres en minuscules.|
    |Type de compte|StorageV2 (usage général v2)|
    |Location| Sélectionnez **USA Est**. |
    |Réplication| Stockage localement redondant (LRS)|
    |Subscription| Sélectionnez votre abonnement|
    |Resource group | Sélectionnez **Utiliser l’existant**, puis *myResourceGroup*.|

### <a name="create-a-file-share-in-the-storage-account"></a>Créer un partage de fichiers dans le compte de stockage

1. Une fois le compte de stockage créé, entrez le nom du compte de stockage dans le champ **Rechercher des ressources, services et documents** en haut du portail. Lorsque le nom de votre compte de stockage apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Fichiers**, comme illustré dans l’image suivante :

   ![Compte de stockage](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Sélectionner **+Partage de fichiers**.
4. Entrez *my-file-share* sous **Nom**, puis sélectionnez **OK**.
5. Fermez la zone **File service** (Service de fichiers).

### <a name="restrict-network-access-to-a-subnet"></a>Restreindre l’accès réseau à un sous-réseau

Par défaut, les comptes de stockage acceptent les connexions réseau provenant des clients de n’importe quel réseau, y compris Internet. Refusez l’accès réseau à partir d’Internet et tous les autres sous-réseaux de tous les réseaux virtuels, à l’exception du sous-réseau *Private* du réseau virtuel *myVirtualNetwork*.

1. Sous **PARAMÈTRES** pour le compte de stockage, sélectionnez **Firewalls and virtual networks** (Pare-feu et réseaux virtuels).
2. Sélectionnez **Réseaux sélectionnés**.
3. Sélectionnez **+Ajouter un réseau virtuel existant**.
4. Sous **Ajouter des réseaux**, sélectionnez les valeurs suivantes puis **Ajouter** :

    |Paramètre|Valeur|
    |----|----|
    |Subscription| Sélectionnez votre abonnement.|
    |Réseaux virtuels|Sélectionnez **myVirtualNetwork** sous **Réseaux virtuels**|
    |Sous-réseaux| Sélectionnez **Private** sous **Sous-réseaux**|

    ![Pare-feu et réseaux virtuels](./media/tutorial-restrict-network-access-to-resources/storage-firewalls-and-virtual-networks.png)

5. Sélectionnez **Enregistrer**.
6. Fermez la zone **Firewalls and virtual networks** (Pare-feu et réseaux virtuels).
7. Sous **PARAMÈTRES** pour le compte de stockage, sélectionnez **Clés d’accès**, comme illustré dans l’image suivante :

      ![Pare-feu et réseaux virtuels](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

8. Notez la valeur **Clé**, car vous devrez l’entrer manuellement dans une étape ultérieure lors du mappage du partage de fichiers à une lettre de lecteur d’une machine virtuelle.

## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Pour tester l’accès réseau à un compte de stockage, déployez une machine virtuelle sur chaque sous-réseau.

### <a name="create-the-first-virtual-machine"></a>Créer la première machine virtuelle

1. Sélectionnez **+ Créer une ressource** en haut à gauche du portail Azure.
2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**.
3. Saisissez ou sélectionnez les informations suivantes, puis sélectionnez **OK** :

   |Paramètre|Valeur|
   |----|----|
   |Nom| myVmPublic|
   |Nom d'utilisateur|Entrez un nom d’utilisateur de votre choix.|
   |Mot de passe| Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Subscription| Sélectionnez votre abonnement.|
   |Resource group| Sélectionnez **Utiliser l’existant**, puis **myResourceGroup**.|
   |Location| Sélectionnez **USA Est**.|

   ![Entrer des informations de base sur une machine virtuelle](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)
4. Sélectionnez une taille de machine virtuelle, puis sélectionnez **Sélectionner**.
5. Sous **Paramètres**, sélectionnez **Réseau** puis **myVirtualNetwork**. Sélectionnez ensuite **Sous-réseau** puis **Public**, comme montré dans l’image suivante :

   ![Sélectionner un réseau virtuel](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)

6. Sous **Groupes de sécurité réseau**, sélectionnez **Avancé**. Le portail crée automatiquement pour vous un groupe de sécurité réseau qui autorise le port 3389, qui devra être ouvert pour vous connecter à la machine virtuelle dans une étape ultérieure. Sélectionnez **OK** sur la page **Paramètres**.
7. Dans la page **Résumé**, sélectionnez **Créer** pour démarrer le déploiement de la machine virtuelle. Le déploiement de la machine virtuelle peut prendre quelques minutes, mais vous pouvez passer à l’étape suivante pendant la création de la machine virtuelle.

### <a name="create-the-second-virtual-machine"></a>Créer la deuxième machine virtuelle

Répétez les étapes 1 à 7, mais à l’étape 3, nommez la machine virtuelle *myVmPrivate* et à l’étape 5, sélectionnez le sous-réseau **Private**.

Le déploiement de la machine virtuelle ne nécessite que quelques minutes. Ne passez pas à l’étape suivante tant que la création n’est pas terminée et que les paramètres ne sont pas ouverts dans le portail.

## <a name="confirm-access-to-storage-account"></a>Vérifier l’accès au compte de stockage

1. Une fois la création de la machine virtuelle *myVmPrivate* terminée, Azure ouvre les paramètres de celle-ci. Connectez-vous à la machine virtuelle en sélectionnant le bouton **Connexion**, comme montré dans l’image suivante :

   ![Connexion à une machine virtuelle](./media/tutorial-restrict-network-access-to-resources/connect-to-virtual-machine.png)

2. Après avoir sélectionné le bouton **Connexion**, le fichier .rdp (Remote Desktop Protocol) est créé et téléchargé sur votre ordinateur.  
3. Ouvrez le fichier .rdp téléchargé. Si vous y êtes invité, sélectionnez **Connexion**. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle. Vous devrez peut-être sélectionner **Plus de choix**, puis **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle. 
4. Sélectionnez **OK**.
5. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez l’avertissement, sélectionnez **Oui** ou **Continuer** pour poursuivre le processus de connexion.
6. Sur la machine virtuelle *myVmPrivate*, mappez le partage de fichiers Azure au lecteur Z à l’aide de PowerShell. Avant d’exécuter les commandes qui suivent, remplacez `<storage-account-key>` et `<storage-account-name>` par les valeurs que vous avez fournies ou récupérées dans [Créer un compte de stockage](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell retourne un résultat semblable à l’exemple suivant :

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Le partage de fichiers Azure est correctement mappé au lecteur Z.

7. Vérifiez que la machine virtuelle ne dispose d’aucune connexion sortante à Internet à partir d’une invite de commande :

   ```
   ping bing.com
   ```

   Vous ne recevez aucune réponse, car le groupe de sécurité réseau associé au sous-réseau *Private* n’autorise pas l’accès sortant à Internet.

8. Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Vérifier que l’accès au compte de stockage est refusé

1. Entrez *myVmPublic* dans le champ **Rechercher des ressources, services et documents** en haut du portail.
2. Quand **myVmPublic** apparaît dans les résultats de la recherche, sélectionnez cette entrée.
3. Répétez les étapes 1 à 6 de [Vérifier l’accès au compte de stockage](#confirm-access-to-storage-account) pour la machine virtuelle *myVmPublic*.

   Après un court délai d’attente, vous recevez une erreur `New-PSDrive : Access is denied`. L’accès est refusé car la machine virtuelle *myVmPublic* est déployée sur le sous-réseau *Public*. Le sous-réseau *Public* ne dispose d’aucun point de terminaison de service activé pour le Stockage Azure. Le compte de stockage permet uniquement l’accès à partir du sous-réseau *Private* et non au sous-réseau *Public*.

4. Fermez les sessions Bureau à distance sur la machine virtuelle *myVmPublic*.

5. Sur votre ordinateur, accédez au [portail](https://portal.azure.com) Azure.
6. Entrez le nom du compte de stockage que vous avez créé dans le champ **Rechercher des ressources, services et documents**. Lorsque le nom de votre compte de stockage apparaît dans les résultats de la recherche, sélectionnez-le.
7. Sélectionnez **Fichiers**.
8. Vous recevez le message d’erreur indiquée dans l’image suivante :

   ![Erreur d’accès refusé](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)

   L’accès est refusé, car votre ordinateur ne se trouve pas dans le sous-réseau *Private* du réseau virtuel *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin du groupe de ressources, supprimez-le ainsi que toutes les ressources qu’il contient :

1. Entrez *myResourceGroup* dans le champ **Recherche** en haut du portail. Quand **myResourceGroup** apparaît dans les résultats de la recherche, sélectionnez-le.
2. Sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans **TAPER NOM DU GROUPE DE RESSOURCES :** puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez activé un point de terminaison de service pour un sous-réseau de réseau virtuel. Vous avez vu que les points de terminaison de service peuvent être activés pour les ressources déployées à partir de différents services Azure. Vous avez créé un compte Stockage Azure et un accès réseau à ce compte de stockage, limité aux ressources du sous-réseau du réseau virtuel. Pour en savoir plus sur les points de terminaison de service, consultez [Points de terminaison de service de réseau virtuel](virtual-network-service-endpoints-overview.md) et [Ajouter, modifier ou supprimer un sous-réseau de réseau virtuel](virtual-network-manage-subnet.md).

Si votre compte comporte plusieurs réseaux virtuels, vous pouvez relier deux réseaux virtuels pour que les ressources de chaque réseau virtuel puissent communiquer entre elles. Pour savoir comment connecter des réseaux virtuels, passez au didacticiel suivant.

> [!div class="nextstepaction"]
> [Connecter des réseaux virtuels](./tutorial-connect-virtual-networks-portal.md)
