---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 950fd82e14902c3a7a94fddb44d50ac372ebe119
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177019"
---
[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Déployer une machine virtuelle

Maintenant que vous avez créé et configuré votre coffre de clés et le jeu de chiffrement de disque, vous pouvez déployer une machine virtuelle à l’aide du chiffrement.
Le processus de déploiement de la machine virtuelle est similaire au processus de déploiement standard, les seules différences sont que vous devez déployer la machine virtuelle dans la même région que vos autres ressources et que vous choisissez d’utiliser une clé gérée par le client.

1. Recherchez **Machines virtuelles** et sélectionnez **+ Ajouter** pour créer une machine virtuelle.
1. Sous l’onglet **De base**, sélectionnez la région de votre jeu de chiffrement de disque et d’Azure Key Vault.
1. Renseignez les autres valeurs du panneau**De base** à votre guise.

    ![Capture d’écran de l’expérience de création de machines virtuelles, dont la valeur de région est mise en surbrillance.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. Sur le panneau **Disques**, sélectionnez **Chiffrement au repos avec une clé gérée par le client**.
1. Sélectionnez votre jeu de chiffrement de disque dans la liste déroulante **Jeu de chiffrement de disque**.
1. Effectuez les sélections restantes comme vous le souhaitez.

    ![Capture d’écran de l’expérience de création de machines virtuelles, dans le panneau Disques. Avec la liste déroulante Jeu de chiffrement de disque mise en surbrillance.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Activer sur un disque existant

> [!CAUTION]
> L’activation du chiffrement de disque sur les disques attachés à une machine virtuelle nécessite l’arrêt de celle-ci.
    
1. Accédez à une machine virtuelle qui se trouve dans la même région que l’un de vos jeux de chiffrement de disque.
1. Ouvrez la machine virtuelle et sélectionnez **Arrêter**.

    ![Capture d’écran de la superposition principale pour votre exemple de machine virtuelle, avec le bouton Arrêter mis en évidence.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. Une fois l’arrêt de la machine virtuelle terminé, sélectionnez **Disques** puis sélectionnez le disque que vous souhaitez chiffrer.

    ![Capture d’écran de votre exemple de machine virtuelle, avec le panneau Disques ouvert. Le disque du système d’exploitation est mis en surbrillance, comme exemple de disque à sélectionner.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Sélectionnez **Chiffrement**, **Chiffrement au repos avec une clé gérée par le client**, puis sélectionnez votre jeu de chiffrement de disque dans la liste déroulante.
1. Sélectionnez **Enregistrer**.

    ![Capture d’écran de votre exemple de disque de système d’exploitation. Le panneau Chiffrement est ouvert, le chiffrement au repos avec une clé gérée par le client est sélectionné, ainsi que votre exemple Azure Key Vault. Après avoir effectué ces sélections, le bouton Enregistrer est sélectionné.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Répétez ce processus pour tous les autres disques attachés à la machine virtuelle que vous souhaitez chiffrer.
1. S’il n’y a pas d’autres disques attachés que vous souhaitez chiffrer lorsque vos disques ont fini de basculer vers les clés gérées par le client, vous pouvez démarrer votre machine virtuelle.
