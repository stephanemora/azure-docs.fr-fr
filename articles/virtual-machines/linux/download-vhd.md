---
title: Télécharger un disque VHD Linux à partir d’Azure
description: Télécharger un disque VHD Linux à l’aide de l’interface de ligne de commande Azure et du portail Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968797"
---
# <a name="download-a-linux-vhd-from-azure"></a>Télécharger un disque VHD Linux à partir d’Azure

Dans cet article, vous allez découvrir comment télécharger un fichier de disque dur virtuel (VHD) Linux à partir d’Azure à l’aide d’Azure CLI et du portail Microsoft Azure. 

Si vous ne l’avez pas déjà fait, installez [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.

Il n’est pas possible de télécharger un disque VHD associé à une machine virtuelle en cours d’exécution à partir d’Azure. Il vous faut arrêter la machine virtuelle pour télécharger un VHD. Si vous souhaitez utiliser un disque VHD en tant [qu’image](tutorial-custom-images.md) pour créer d’autres machines virtuelles avec de nouveaux disques, vous devez déprovisionner et généraliser le système d’exploitation contenu dans le fichier, puis arrêter la machine virtuelle. Pour utiliser le VHD en tant que disque d’une nouvelle instance d’une machine virtuelle ou d’un disque de données existant, il vous suffit d’arrêter et de libérer de la machine virtuelle.

Pour utiliser le VHD en tant qu’image pour créer d’autres machines virtuelles, suivez les étapes ci-dessous :

1. Utilisez SSH, le nom du compte et l’adresse IP publique de la machine virtuelle pour vous y connecter et la déprovisionner. Recherchez l’adresse IP publique avec la commande [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Le paramètre +user supprime également le dernier compte d’utilisateur approvisionné. Si vous sauvegardez les informations d’identification du compte sur la machine virtuelle, n’insérez pas ce paramètre +user. L’exemple suivant permet de supprimer le dernier compte d’utilisateur approvisionné :

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Connectez-vous à votre compte Azure avec [az login](https://docs.microsoft.com/cli/azure/reference-index).
3. Arrêtez et libérez la machine virtuelle.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Généralisez la machine virtuelle. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Pour utiliser le VHD en tant que disque d’une nouvelle instance d’une machine virtuelle ou d’un disque de données existant, suivez les étapes ci-dessous :

1.  Connectez-vous au [portail Azure](https://portal.azure.com/).
2.  Dans le menu de gauche, sélectionnez **Machines virtuelles**.
3.  Sélectionnez la machine virtuelle dans la liste.
4.  Sur la page de la machine virtuelle, sélectionnez **Arrêter**.

    ![Arrêter la machine virtuelle](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Générer une URL de SAP

Pour télécharger le fichier VHD, vous devez générer une URL de [signature d’accès partagé (SAP)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Un délai d’expiration est affecté à l’URL lors de sa génération.

1.  Dans le menu du page pour la machine virtuelle, sélectionnez **Disques**.
2.  Sélectionnez le disque de système d’exploitation de la machine virtuelle, puis **Exportation de disque**.
3.  Sélectionnez **Générer une URL**.

    ![Générer une URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Télécharger un VHD

1.  Sous l’URL générée, sélectionnez **Télécharger le fichier de disque dur virtuel**.
**
    ![Télécharger un VHD](./media/download-vhd/export-download.png)

2.  Vous devrez peut-être sélectionner **Enregistrer** dans le navigateur pour commencer le téléchargement. Le nom par défaut du fichier VHD est *abcd*.

    ![Sélectionnez Enregistrer dans le navigateur](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [charger et créer une machine virtuelle Linux à partir d’un disque personnalisé avec Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gestion des disques Azure avec l’interface de ligne de commande Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

