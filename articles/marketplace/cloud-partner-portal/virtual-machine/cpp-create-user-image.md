---
title: Créer une image de machine virtuelle d’utilisateur pour la Place de marché Azure
description: Répertorie les étapes et les références nécessaires pour créer une image de machine virtuelle d’utilisateur.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 49db8c6717cd26886c3b49f8c99fdd2b08e8713d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278005"
---
# <a name="create-a-user-vm-image"></a>Créer une image de machine virtuelle d’utilisateur

Cet article explique les deux étapes générales requises pour créer une image non managée à partir d’un disque dur virtuel généralisé.  Les références sont fournies pour vous guider durant chaque étape : capturer l’image et généraliser l’image.


## <a name="capture-the-vm-image"></a>Capturer l’image de machine virtuelle

Suivez les instructions de l’article suivant sur la capture de la machine virtuelle qui correspond à votre méthode d’accès :

-  PowerShell : [Comment créer une image de machine virtuelle non managée à partir d’une machine virtuelle Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI : [Créer une image d’une machine virtuelle ou d’un disque dur virtuel](../../../virtual-machines/linux/capture-image.md)
-  API : [Virtual Machines - Capture](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture) (Machines virtuelles : capturer)


## <a name="generalize-the-vm-image"></a>Généraliser l’image de machine virtuelle

Étant donné que vous avez généré l’image utilisateur à partir d’un disque dur virtuel généralisé précédemment, elle doit également être généralisée.  Là encore, sélectionnez l’article suivant qui correspond à votre méthode d’accès.  (Il se peut que vous ayez déjà généralisé votre disque lorsque vous l’avez capturé.)

-  PowerShell : [Généraliser la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI : [Étape 2 : Créer une image de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API : [Virtual Machines - Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize) (Machines virtuelles : généraliser)


## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous allez [créer un certificat](cpp-create-key-vault-cert.md) et le stocker dans un Azure Key Vault.  Ce certificat est requis pour établir une connexion sécurisée de WinRM à la machine virtuelle.
