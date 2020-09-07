---
title: Créer des images de machine virtuelle pour votre appareil GPU Azure Stack Edge
description: Décrit comment créer des images de machine virtuelle Linux ou Windows à utiliser avec votre appareil GPU Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 83332c3bfa0b2b99d7333fa679fb8d398aecf8bd
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268908"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-device"></a>Créer des images de machine virtuelle personnalisées pour votre appareil Azure Stack Edge

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Pour déployer des machines virtuelles sur votre appareil Azure Stack Edge, vous devez être capable de créer des images de machine virtuelle personnalisées que vous pouvez utiliser pour créer des machines virtuelles. Cet article décrit les étapes nécessaires à la création d’images personnalisées de machines virtuelles Linux ou Windows que vous pouvez utiliser pour déployer des machines virtuelles sur votre appareil Azure Stack Edge.

## <a name="vm-image-workflow"></a>Workflow d’image de machine virtuelle

Le workflow nécessite la création d’une machine virtuelle dans Azure, la personnalisation de la machine virtuelle, la généralisation, puis le téléchargement du disque dur virtuel correspondant à cette machine virtuelle. Ce disque dur virtuel généralisé est chargé sur Azure Stack Edge, le disque managé est créé à partir de ce disque dur virtuel, l’image est créée à partir du disque managé et, pour finir, les machines virtuelles sont créées à partir de cette image.   

Pour plus d’informations, consultez [Déployer une machine virtuelle sur votre appareil Azure Stack Edge à l’aide d’Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).


## <a name="create-a-windows-custom-vm-image"></a>Créer une image de machine virtuelle personnalisée Windows

Effectuez les étapes suivantes pour créer une image de machine virtuelle Windows.

1. Créez une machine virtuelle Windows. Pour plus d'informations, consultez [Tutoriel : Créer et gérer des machines virtuelles Windows avec Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md)

2. Téléchargez un disque de système d’exploitation existant.

    - Suivez les étapes décrites dans [Télécharger un disque dur virtuel](../virtual-machines/windows/download-vhd.md).

    - Utilisez la commande `sysprep` suivante à la place de ce qui est décrit dans la procédure précédente.
    
        `c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm`
   
       Vous pouvez également consulter [Vue d’ensemble de Sysprep (préparation du système)](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Utilisez ce disque dur virtuel pour créer et déployer une machine virtuelle sur votre appareil Azure Stack Edge.

## <a name="create-a-linux-custom-vm-image"></a>Créer une image de machine virtuelle personnalisée Linux

Effectuez les étapes suivantes pour créer une image de machine virtuelle Linux.

1. Créez une machine virtuelle Linux. Pour plus d'informations, consultez [Tutoriel : Créer et gérer des machines virtuelles Linux avec l’interface Azure CLI](../virtual-machines/linux/tutorial-manage-vm.md).

2. [Téléchargez un disque de système d’exploitation existant](../virtual-machines/linux/download-vhd.md).

Utilisez ce disque dur virtuel pour créer et déployer une machine virtuelle sur votre appareil Azure Stack Edge. Vous pouvez utiliser les deux images de la Place de marché Azure suivantes pour créer des images personnalisées Linux :

|Nom de l’élément  |Description  |Serveur de publication  |
|---------|---------|---------|
|[Serveur Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.ubuntuserver) |Ubuntu Server est le Linux le plus populaire au monde pour les environnements cloud.|Canonical|
|[Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian) |Debian GNU/Linux est l’une des distributions Linux les plus populaires.     |credativ|

Pour obtenir la liste complète des images de la Place de marché Azure qui peuvent fonctionner (actuellement non testées), accédez à [Éléments de Place de Marché Azure disponibles pour Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910).


## <a name="next-steps"></a>Étapes suivantes

[Déployer des machines virtuelles sur votre appareil Azure Stack Edge](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
