---
title: Créer une image de machine virtuelle d’utilisateur pour la Place de marché Azure
description: Répertorie les étapes et les références nécessaires pour créer une image de machine virtuelle d’utilisateur.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 6bbee7f53cb9a61b72bdbbd941a3a0401f5b913b
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273951"
---
# <a name="create-a-user-vm-image"></a>Créer une image de machine virtuelle d’utilisateur

> [!IMPORTANT]
> Depuis le 13 avril 2020, nous avons commencé à déplacer la gestion de vos offres de machines virtuelles Azure vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions données fournies dans [Créer vos ressources techniques de machine virtuelle Azure](https://aka.ms/AzureVMTechAsset) pour gérer vos offres migrées.

Cet article explique les deux étapes générales requises pour créer une image non managée à partir d’un disque dur virtuel généralisé.  Les références sont fournies pour vous guider durant chaque étape : capturer l’image et généraliser l’image.


## <a name="capture-the-vm-image"></a>Capturer l’image de machine virtuelle

Suivez les instructions de l’article suivant sur la capture de la machine virtuelle qui correspond à votre méthode d’accès :

-  PowerShell : [Comment créer une image de machine virtuelle non managée à partir d’une machine virtuelle Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI : [Créer une image d’une machine virtuelle ou d’un disque dur virtuel](../../../virtual-machines/linux/capture-image.md)
-  API : [Machines virtuelles : capturer](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Généraliser l’image de machine virtuelle

Étant donné que vous avez généré l’image utilisateur à partir d’un disque dur virtuel généralisé précédemment, elle doit également être généralisée.  Là encore, sélectionnez l’article suivant qui correspond à votre méthode d’accès.  (Il se peut que vous ayez déjà généralisé votre disque lorsque vous l’avez capturé.)

-  PowerShell : [Généraliser la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI : [Étape 2 : Créer une image de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API : [Machines virtuelles : généraliser](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous allez [créer un certificat](cpp-create-key-vault-cert.md) et le stocker dans un Azure Key Vault.  Ce certificat est requis pour établir une connexion sécurisée de WinRM à la machine virtuelle.
