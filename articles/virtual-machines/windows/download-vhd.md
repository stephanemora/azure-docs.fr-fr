---
title: Télécharger un VHD Windows à partir d’Azure
description: Téléchargez un VHD Windows à l’aide du Portail Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75940449"
---
# <a name="download-a-windows-vhd-from-azure"></a>Télécharger un VHD Windows à partir d’Azure

Dans cet article, vous apprendrez à télécharger un fichier de disque dur virtuel (VHD) Windows à partir d’Azure à l’aide du Portail Azure.

## <a name="optional-generalize-the-vm"></a>Facultatif : Généraliser la machine virtuelle

Si vous souhaitez utiliser le disque dur virtuel (VHD) en tant qu’[image](tutorial-custom-images.md) pour créer d’autres machines virtuelles, vous devez utiliser [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) pour généraliser le système d’exploitation. 

Pour utiliser le VHD en tant qu’image pour créer d’autres machines virtuelles, généralisez la machine virtuelle.

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com/).
2. [Connectez-vous à la machine virtuelle](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Sur la machine virtuelle, ouvrez la fenêtre d’invite de commandes en tant qu’administrateur.
4. Remplacez le répertoire par *%windir%\system32\sysprep* et exécutez sysprep.exe.
5. Dans la boîte de dialogue Outil de préparation du système, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience) du système** et vérifiez que **Généraliser** est sélectionné.
6. Dans Options d’arrêt, sélectionnez **Arrêter**, puis cliquez sur **OK**. 


## <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.

Il n’est pas possible de télécharger un disque VHD associé à une machine virtuelle en cours d’exécution à partir d’Azure. Il vous faut arrêter la machine virtuelle pour télécharger un VHD. 

1. Dans le menu Hub du Portail Azure, cliquez sur **Machines virtuelles**.
1. Sélectionnez la machine virtuelle dans la liste.
1. Dans le panneau de la machine virtuelle, cliquez sur **Arrêter**.


## <a name="generate-download-url"></a>Générer l’URL de téléchargement

Pour télécharger le fichier VHD, vous devez générer une URL de [signature d’accès partagé (SAP)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Un délai d’expiration est affecté à l’URL lors de sa génération.

1. Sur la page de la machine virtuelle, cliquez sur **Disques** dans le menu de gauche.
1. Sélectionnez le disque du système d’exploitation de la machine virtuelle.
1. Sur la page du disque, sélectionnez **Exportation de disque** dans le menu de gauche.
1. Le délai d’expiration par défaut de l’URL est *3 600* secondes. Augmentez cette valeur à **36 000** pour les disques du système d’exploitation Windows.
1. Cliquez sur **Générer l’URL**.

> [!NOTE]
> Le délai d’expiration est augmenté par rapport à la valeur par défaut afin de laisser suffisamment de temps pour télécharger le fichier volumineux de VHD pour un système d’exploitation Windows Server. En général, le téléchargement d’un fichier de VHD contenant le système d’exploitation Windows prend plusieurs heures, en fonction de la connexion. Si vous téléchargez un VHD pour un disque de données, le délai par défaut est suffisant. 
> 
> 

## <a name="download-vhd"></a>Télécharger un VHD

1. Sous l’URL générée, cliquez sur Télécharger le fichier de disque dur virtuel.
1. Vous devrez peut-être cliquer sur **Enregistrer** dans votre navigateur pour commencer le téléchargement. Le nom par défaut du fichier VHD est *abcd*.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [charger un fichier de VHD sur Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Créez des disques managés à partir de disques non managés dans un compte de stockage](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Gérez des disques Azure avec PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

