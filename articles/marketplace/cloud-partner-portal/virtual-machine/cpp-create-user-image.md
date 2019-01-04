---
title: Créer une image de machine virtuelle d’utilisateur pour Place de marché Microsoft Azure | Microsoft Docs
description: Répertorie les étapes et les références nécessaires pour créer une image de machine virtuelle d’utilisateur.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196120"
---
# <a name="create-a-user-vm-image"></a>Créer une image de machine virtuelle d’utilisateur

Cet article explique les deux étapes générales requises pour créer une image non managée à partir d’un disque dur virtuel généralisé.  Les références sont fournies pour vous guider durant chaque étape : capturer l’image et généraliser l’image.


## <a name="capture-the-vm-image"></a>Capturer l’image de machine virtuelle

Suivez les instructions de l’article suivant sur la capture de la machine virtuelle qui correspond à votre méthode d’accès :

-  PowerShell : [Comment créer une image de machine virtuelle non managée à partir d’une machine virtuelle Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Interface de ligne de commande Azure : [Créer une image d’une machine virtuelle ou d’un disque dur virtuel](../../../virtual-machines/linux/capture-image.md)
-  API : [Machines virtuelles : capturer](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Généraliser l’image de machine virtuelle

Étant donné que vous avez généré l’image utilisateur à partir d’un disque dur virtuel généralisé précédemment, elle doit également être généralisée.  Là encore, sélectionnez l’article suivant qui correspond à votre méthode d’accès.  (Il se peut que vous ayez déjà généralisé votre disque lorsque vous l’avez capturé.)

-  PowerShell : [Généraliser la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interface de ligne de commande Azure : [Étape 2 : Créer une image de machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API : [Machines virtuelles : généraliser](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous allez [créer un certificat](cpp-create-key-vault-cert.md) et le stocker dans un Azure Key Vault.  Ce certificat est requis pour établir une connexion sécurisée de WinRM à la machine virtuelle.
