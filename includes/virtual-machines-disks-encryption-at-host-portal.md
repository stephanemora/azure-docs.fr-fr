---
title: Fichier Include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f0a706cc51027bc503da9ee3bc637216e9e52853
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171370"
---
## <a name="restrictions"></a>Restrictions

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Régions prises en charge

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Tailles des machines virtuelles prises en charge

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Prérequis

Afin de pouvoir utiliser le chiffrement sur l'hôte pour vos machines virtuelles ou groupes de machines virtuelles identiques, vous devez activer la fonctionnalité dans votre abonnement. Envoyez-nous un e-mail à l’adresse encryptionAtHost@microsoft. com avec vos ID d’abonnement pour activer la fonctionnalité.

Connectez-vous au portail Azure à l’aide du [lien fourni](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Pour accéder au portail Azure, vous devez utiliser le [lien fourni](https://aka.ms/diskencryptionupdates). Le chiffrement au niveau de l’hôte n’est actuellement pas visible dans le portail Azure public si le lien n’est pas utilisé.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Créer un coffre de clés Azure et un jeu de chiffrement de disque

Une fois la fonctionnalité activée, vous devez configurer un coffre de clés Azure et un jeu de chiffrement de disque, si ce n’est pas déjà fait.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Déployer une machine virtuelle

Vous devez déployer une nouvelle machine virtuelle pour activer le chiffrement au niveau de l’hôte, car il ne peut pas être activé sur les machines virtuelles existantes.

1. Recherchez **Machines virtuelles** et sélectionnez **+ Ajouter** pour créer une machine virtuelle.
1. Créez une nouvelle machine virtuelle, sélectionnez une région appropriée et une taille de machine virtuelle prise en charge.
1. Renseignez les autres valeurs du panneau **Informations de base** à votre guise, puis accédez au panneau **Disques**.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Capture d’écran du panneau Informations de base de la création d’une machine virtuelle, la région et la taille de machine virtuelle sont mises en surbrillance.":::

1. Dans le panneau **Disques**, sélectionnez **Oui** pour **Chiffrement au niveau de l’hôte**.
1. Effectuez les sélections restantes comme vous le souhaitez.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="Capture d’écran du panneau Disques de la création d’une machine virtuelle, l’option Chiffrement au niveau de l’hôte est mise en surbrillance.":::

1. Terminez le processus de déploiement de la machine virtuelle en effectuant les sélections qui conviennent à votre environnement.

Vous avez maintenant déployé une machine virtuelle avec chiffrement au niveau de l’hôte, et tous les disques associés seront chiffrés à l’aide du chiffrement au niveau de l’hôte.