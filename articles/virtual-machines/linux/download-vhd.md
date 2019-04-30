---
title: Télécharger un disque VHD Linux à partir d’Azure | Microsoft Docs
description: Télécharger un disque VHD Linux à l’aide de l’interface de ligne de commande Azure et du portail Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: f72d49a3ab204ce64eb89d0f05630b640c138e0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61390226"
---
# <a name="download-a-linux-vhd-from-azure"></a>Télécharger un disque VHD Linux à partir d’Azure

Dans cet article, vous allez découvrir comment télécharger un fichier de disque dur virtuel (VHD) Linux à partir d’Azure à l’aide d’Azure CLI et du portail Microsoft Azure. 

Si vous ne l’avez pas déjà fait, installez [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.

Il n’est pas possible de télécharger un disque VHD associé à une machine virtuelle en cours d’exécution à partir d’Azure. Vous devez arrêter la machine virtuelle pour télécharger un disque VHD. Si vous souhaitez utiliser un disque VHD en tant [qu’image](tutorial-custom-images.md) pour créer d’autres machines virtuelles avec de nouveaux disques, vous devez déprovisionner et généraliser le système d’exploitation contenu dans le fichier, puis arrêter la machine virtuelle. Pour utiliser le disque VHD en tant que disque d’une nouvelle instance d’une machine virtuelle ou d’un disque de données existant, il vous suffit d’arrêter et de libérer la machine virtuelle.

Pour utiliser le disque VHD en tant qu’image pour créer d’autres machines virtuelles, suivez les étapes ci-dessous :

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

Pour utiliser le disque VHD en tant que disque d’une nouvelle instance d’une machine virtuelle ou d’un disque de données existant, suivez les étapes ci-dessous :

1.  Connectez-vous au [Portail Azure](https://portal.azure.com/).
2.  Dans le menu Hub, cliquez sur **Machines virtuelles**.
3.  Sélectionnez la machine virtuelle dans la liste.
4.  Dans le panneau de la machine virtuelle, cliquez sur **Arrêter**.

    ![Arrêter la machine virtuelle](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Générer une URL de SAP

Pour télécharger le fichier VHD, vous devez générer une URL de [signature d’accès partagé (SAP)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Un délai d’expiration est affecté à l’URL lors de sa génération.

1.  Dans le menu du panneau de la machine virtuelle, cliquez sur **Disques**.
2.  Sélectionnez le disque de système d’exploitation de la machine virtuelle, puis cliquez sur **Exporter**.
3.  Cliquez sur **Générer l’URL**.

    ![Générer une URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Télécharger un disque VHD

1.  Sous l’URL générée, cliquez sur Télécharger le fichier de disque dur virtuel.

    ![Télécharger un VHD](./media/download-vhd/export-download.png)

2.  Vous devrez peut-être cliquer sur **Enregistrer** dans le navigateur pour commencer le téléchargement. Le nom par défaut du fichier VHD est *abcd*.

    ![Cliquez sur Enregistrer dans le navigateur](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [charger et créer une machine virtuelle Linux à partir d’un disque personnalisé avec Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Gestion des disques Azure avec l’interface de ligne de commande Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

