---
title: Connexion privée à un compte de stockage à l’aide d’Azure Private Endpoint
description: Découvrez comment vous connecter en privé à un compte de stockage dans Azure à l’aide de Private Endpoint.
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: bb0c6e9d20c12df3532a52df1fe4d9574344d4b3
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104729"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Connexion privée à un compte de stockage à l’aide d’Azure Private Endpoint
Azure Private Endpoint est le composant fondamental de Private Link dans Azure. Il permet à des ressources Azure, comme des machines virtuelles, de communiquer en privé avec des ressources Private Link.

Dans ce guide de démarrage rapide, vous allez apprendre à créer une machine virtuelle sur un réseau virtuel Azure, un compte de stockage avec Private Endpoint à l’aide du portail Azure. Ensuite, vous pouvez accéder en toute sécurité au compte de stockage à partir de la machine virtuelle.


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="create-a-vm"></a>Créer une machine virtuelle
Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource Private Link (un compte de stockage pour cet exemple).

### <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et le sous-réseau pour héberger la machine virtuelle qui est utilisée pour accéder à votre ressource Private Link.

1. Dans le coin supérieur gauche de l’écran, sélectionnez **Créer une ressource** > **Mise en réseau** > **Réseau virtuel**.
1. Dans **Créer un réseau virtuel**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Nom | Entrez *MyVirtualNetwork*. |
    | Espace d’adressage | Entrez *10.1.0.0/16*. |
    | Subscription | Sélectionnez votre abonnement.|
    | Resource group | Sélectionnez **Créer nouveau**, entrez *myResourceGroup* et sélectionnez **OK**. |
    | Location | Sélectionnez **WestCentralUS**.|
    | Sous-réseau - Nom | Entrez *mySubnet*. |
    | Plage d’adresses du sous-réseau | Entrez *10.1.0.0/24*. |
    |||
1. Conservez les autres valeurs par défaut, puis sélectionnez **Créer**.


### <a name="create-virtual-machine"></a>Créer une machine virtuelle

1. En haut à gauche de l’écran du portail Azure, sélectionnez **Créer une ressource** > **Calcul** > **Machine virtuelle**.

1. Dans **Créer une machine virtuelle - Notions de base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.  |
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom de la machine virtuelle | Entrez *myVm*. |
    | Région | Sélectionnez **WestCentralUS**. |
    | Options de disponibilité | Conservez la valeur par défaut **Aucune redondance d’infrastructure nécessaire**. |
    | Image | Sélectionnez **Windows Server 2019 Datacenter**. |
    | Size | Conservez la valeur par défaut **Standard DS1 v2**. |
    | **COMPTE ADMINISTRATEUR** |  |
    | Nom d’utilisateur | Entrez un nom d’utilisateur de votre choix. |
    | Mot de passe | Entrez un mot de passe de votre choix. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité définies](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmer le mot de passe | Retapez le mot de passe. |
    | **RÈGLES DES PORTS D’ENTRÉE** |  |
    | Aucun port d’entrée public | Conservez la valeur par défaut **Aucun**. |
    | **ÉCONOMISEZ DE L’ARGENT** |  |
    | Vous disposez déjà d’une licence Windows ? | Conservez la valeur par défaut **Non**. |
    |||

1. Sélectionnez **Suivant : Disques**.

1. Dans **Créer une machine virtuelle - Disks**, conservez les valeurs par défaut et sélectionnez **Suivant : Mise en réseau**.

1. Dans **Créer une machine virtuelle - Mise en réseau**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | Réseau virtuel | Conservez la valeur par défaut, **MyVirtualNetwork**.  |
    | Espace d’adressage | Conservez la valeur par défaut, **10.1.0.0/24**.|
    | Subnet | Conservez la valeur par défaut, **mySubnet (10.1.0.0/24)** .|
    | Adresse IP publique | Conservez la valeur par défaut **(new) myVm-ip**. |
    | Aucun port d’entrée public | Sélectionnez **Autoriser les ports sélectionnés**. |
    | Sélectionner des ports d’entrée | Sélectionnez **HTTP** et **RDP**.|
    ||

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers la page **Vérifier + créer** où Azure valide votre configuration.

1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer**.

## <a name="create-your-private-endpoint"></a>Créer votre instance Private Endpoint
Dans cette section, vous allez créer un compte de stockage privé à l’aide de Private Endpoint. 

1. Dans le coin supérieur gauche de l’écran dans le portail Azure, sélectionnez **Créer une ressource** > **Stockage** > **Compte de stockage**.

1. Dans **Créer un compte de stockage - De base**, entrez ou sélectionnez ces informations :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    | **DÉTAILS DE L’INSTANCE** |  |
    | Nom du compte de stockage  | Entrez *mystorageaccount*. Si ce nom est utilisé, créez un nom unique. |
    | Région | Sélectionnez **WestCentralUS**. |
    | Performances| Conservez la valeur par défaut **Standard**. |
    | Type de compte | Conservez la valeur par défaut **Stockage (usage général v2)** . |
    | Réplication | Sélectionnez **Stockage géo-redondant avec accès en lecture (RA-GRS)** . |
    |||
  
3. Sélectionnez **Suivant : Mise en réseau**.
4. Dans  **Créer un compte de stockage - Mise en réseau**, pour la méthode de connectivité, sélectionnez **Private Endpoint**.
5. Dans  **Créer un compte de stockage - Mise en réseau**, sélectionnez **Ajouter Private Endpoint**. 
6. Dans **Créer Private Endpoint**, entrez ou sélectionnez les informations suivantes :

    | Paramètre | Valeur |
    | ------- | ----- |
    | **DÉTAILS DU PROJET** | |
    | Subscription | Sélectionnez votre abonnement. |
    | Resource group | Sélectionnez **myResourceGroup**. Vous avez créé cela dans la section précédente.|
    |Location|Sélectionnez **WestCentralUS**.|
    |Nom|Entrez *myPrivateEndpoint*.  |
    |Sous-ressource de stockage|Conservez la valeur de **Blob** par défaut. |
    | **MISE EN RÉSEAU** |  |
    | Réseau virtuel  | Sélectionnez  *MyVirtualNetwork* dans le groupe de ressources *myResourceGroup*. |
    | Subnet | Sélectionnez *mySubnet*. |
    | **INTÉGRATION À DNS PRIVÉ**|  |
    | Intégrer à une zone DNS privée  | Conservez la valeur par défaut **Oui**. |
    | Zone DNS privée  | Conservez la valeur par défaut ** (New) privatelink.blob.core.windows.net**. |
    |||
7. Sélectionnez **OK**. 
8. Sélectionnez **Vérifier + créer**. Vous êtes redirigé vers la page  **Vérifier + créer**  où Azure valide votre configuration. 
9. Quand le message  **Validation réussie** s’affiche, sélectionnez  **Créer**. 
10. Accédez à la ressource de compte de stockage que vous venez de créer.
11. Sélectionnez  **Clés d’accès** dans le menu de contenu de gauche.
12. Sélectionnez **Copier** sur la chaîne de connexion pour key1.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Se connecter à une machine virtuelle à partir d’Internet

Connectez-vous à la machine virtuelle *myVm* à partir d’Internet comme suit :

1. Dans la barre de recherche du portail, entrez *myVm*.

1. Sélectionnez le bouton **Connexion**. Après avoir sélectionné le bouton **Connecter**, **Se connecter à la machine virtuelle** s’ouvre.

1. Sélectionnez **Télécharger le fichier RDP**. Azure crée un fichier de protocole RDP (Remote Desktop Protocol) ( *.rdp*) et le télécharge sur votre ordinateur.

1. Ouvrez le fichier .rdp* téléchargé.

    1. Si vous y êtes invité, sélectionnez **Connexion**.

    1. Entrez le nom d’utilisateur et le mot de passe spécifiés lors de la création de la machine virtuelle.

        > [!NOTE]
        > Vous devrez peut-être sélectionner **Plus de choix** > **Utiliser un autre compte**, pour spécifier les informations d’identification que vous avez entrées lorsque vous avez créé la machine virtuelle.

1. Sélectionnez **OK**.

1. Un avertissement de certificat peut s’afficher pendant le processus de connexion. Si vous recevez un avertissement de certificat, sélectionnez **Oui** ou **Continuer**.

1. Une fois que le bureau de la machine virtuelle s’affiche, réduisez-le pour revenir à votre poste de travail local.  

## <a name="access-storage-account-privately-from-the-vm"></a>Accéder au compte de stockage en privé à partir de la machine virtuelle

Dans cette section, vous allez vous connecter en privé au compte de stockage à l’aide de Private Endpoint.

> [!IMPORTANT]
> La configuration DNS pour le stockage nécessite une modification manuelle du fichier hosts pour inclure le nom de domaine complet du compte spécifique. Modifiez le fichier suivant en utilisant les autorisations d’administrateur sur Windows : c:\Windows\System32\Drivers\etc\hosts ou Linux /etc/hosts. Incluez les informations DNS pour le compte de l’étape précédente au format [Adresse IP privée] myaccount.blob.core.windows.net

1. Dans le Bureau à distance de  *myVM*, ouvrez PowerShell.
2. Entrez `nslookup mystorageaccount.blob.core.windows.net` Vous recevez un message similaire à celui-ci :
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
3. Install [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Select **Storage accounts** with the right-click.
5. Select **Connect to an azure storage**.
6. Select **Use a connection string**.
7. Select **Next**.
8. Enter the connection string by pasting the information previously copied.
9. Select **Next**.
10. Select **Connect**.
11. Browse the Blob containers from mystorageaccount 
12. (Optionally) Create folders and/or upload files to *mystorageaccount*. 
13. Close the remote desktop connection to *myVM*. 

Additional options to access the storage account:
- Microsoft Azure Storage Explorer is a standalone free app from Microsoft that enables you to work visually with Azure storage data on Windows, macOS, and Linux. You can install the application to browse privately the storage account content. 
 
- The AzCopy utility is another option for high-performance scriptable data transfer for Azure storage. Use AzCopy to transfer data to and from Blob, File, and Table storage. 


## Clean up resources 
When you're done using the Private Endpoint, storage account and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter *myResourceGroup* for **TYPE THE RESOURCE GROUP NAME** and select **Delete**. 

## Next steps
In this Quickstart, you created a VM on a virtual network and storage account and a Private Endpoint. You connected to one VM from the internet and securely communicated to the storage account using Private Link. To learn more about Private Endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
