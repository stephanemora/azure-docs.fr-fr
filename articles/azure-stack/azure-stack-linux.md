---
title: Ajouter des images Linux à Azure Stack
description: Découvrez comment ajouter des images Linux à Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: bee143f7e814884eda3c5dc9017895a8431e76aa
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890550"
---
# <a name="add-linux-images-to-azure-stack"></a>Ajouter des images Linux à Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez déployer des machines virtuelles Linux sur Azure Stack en ajoutant une image Linux dans la Place de marché Azure Stack. Le moyen le plus simple d’ajouter une image Linux à Azure Stack est à travers la gestion de la Place de marché. Ces images ont été préparées et testées pour leur compatibilité avec Azure Stack.

## <a name="marketplace-management"></a>Gestion de la Place de marché

Pour télécharger des images Linux à partir de la Place de Marché Azure, utilisez l’article [Télécharger des éléments de la Place de Marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md). Sélectionnez les images Linux que vous souhaitez proposer aux utilisateurs sur votre système Azure Stack. 

Notez qu’il existe des mises à jour fréquentes pour ces images, aussi reportez-vous régulièrement à la Gestion de la Place de marché pour les maintenir à jour.

## <a name="prepare-your-own-image"></a>Préparer votre propre image

Dans la mesure du possible, téléchargez les images disponibles via la Gestion de la Place de marché qui ont été préparées et testées pour Azure Stack. 
 
L’agent Azure Linux (généralement appelé `WALinuxAgent` ou `walinuxagent`) est requis, et toutes les versions de l’agent ne fonctionnent pas sur Azure Stack. Vous devez utiliser la version 2.2.20 ou une version ultérieure si vous créez votre propre image. Notez que [cloud-init](https://cloud-init.io/) n’est pas pris en charge sur Azure Stack pour l’instant.

Vous pouvez préparer votre propre image Linux en appliquant les instructions suivantes :

* [Distributions CentOS](../virtual-machines/linux/create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](../virtual-machines/linux/debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES et openSUSE](../virtual-machines/linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Serveur Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    
## <a name="add-your-image-to-the-marketplace"></a>Ajouter l’image à la Place de marché
 
Suivez [Ajoutez l’image à la Place de Marché](azure-stack-add-vm-image.md). Vérifiez que le paramètre `OSType` a la valeur `Linux`.

Une fois que vous avez ajouté l’image à la Place de marché, un élément de Place de marché est créé et les utilisateurs peuvent déployer une machine virtuelle Linux.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Télécharger des éléments de la Place de marché à partir d’Azure dans Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Vue d’ensemble de la Place de Marché Azure Stack](azure-stack-marketplace.md)
