---
title: Fichier Include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8313259d5375bb19e7caa08a01a658707453d133
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87177018"
---
## <a name="supported-regions"></a>Régions prises en charge

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="getting-started"></a>Prise en main

1. Connectez-vous au [portail Azure](https://aka.ms/diskencryptionupdates).

    > [!IMPORTANT]
    > Pour accéder au portail Azure, vous devez utiliser le [lien fourni](https://aka.ms/diskencryptionupdates). Le double chiffrement au repos n’est actuellement pas visible dans le portail Azure public sans utiliser le lien.

1. Recherchez **Jeux de chiffrement de disque**, puis sélectionnez le résultat.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-encryption-sets-search.png" alt-text="Capture d’écran du portail Azure principal, avec « Jeux de chiffrement de disque » en surbrillance dans la barre de recherche.":::

1. Sélectionnez **Ajouter**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-add-disk-encryption-set.png" alt-text="Capture d’écran du panneau Jeux de chiffrement de disque, avec le bouton + Ajouter sélectionné.":::

1. Sélectionnez une des régions prises en charge.
1. Pour **Type de chiffrement**, sélectionnez **Double chiffrement avec les clés gérées par la plateforme et gérées par le client**.

    > [!NOTE]
    > Une fois que vous avez créé un jeu de chiffrement de disque avec un type de chiffrement particulier, il n’est plus possible de le modifier. Si vous souhaitez utiliser un type de chiffrement différent, vous devez créer un nouveau jeu de chiffrement de disque.

1. Entrez les informations restantes.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-create-disk-encryption-set-blade.png" alt-text="Capture d’écran du panneau de création du jeu de chiffrement de disque, avec les options Régions et Double chiffrement avec les clés gérées par la plateforme et gérées par le client en surbrillance":::

1. Sélectionnez un Azure Key Vault et une clé, ou créez-en une si nécessaire.

    > [!NOTE]
    > Lorsque vous créez une instance Key Vault, vous devez activer la suppression réversible et la protection contre le vidage. Ces paramètres sont obligatoires lors de l’utilisation d’un Key Vault pour le chiffrement des disques managés, et vous protègent contre la perte de données en raison d’une suppression accidentelle.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-select-key-vault.png" alt-text="Capture d’écran du panneau de création du Key Vault.":::

1. Sélectionnez **Create** (Créer).
1. Accédez au jeu de chiffrement de disque que vous avez créé, puis sélectionnez l’erreur qui s’affiche. Cela a pour effet de configurer votre jeu de chiffrement de disque pour travailler.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-disk-set-error.png" alt-text="Capture d’écran de l’erreur affichée concernant le jeu de chiffrement de disque ; le texte de l’erreur est le suivant : « Pour associer un disque, une image ou un instantané à ce jeu de chiffrement de disque, vous devez octroyer des autorisations au coffre de clés. »":::

    Une notification doit s’afficher. Cela vous permettra d’utiliser le jeu de chiffrement de disque avec votre coffre de clés.
    
    ![Capture d’écran de l’autorisation et de l’attribution de rôle réussie pour votre coffre de clés.](media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)

1. Accédez à votre disque.
1. Sélectionnez **Chiffrement**.
1. Pour **Type de chiffrement**, sélectionnez **Double chiffrement avec les clés gérées par la plateforme et gérées par le client**.
1. Sélectionnez votre du jeu de chiffrement de disque
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/virtual-machines-disks-double-encryption-at-rest-portal/double-encryption-enable-disk-blade.png" alt-text="Capture d’écran du panneau de chiffrement pour votre disque managé, le type de chiffrement susmentionné est mis en surbrillance.":::

Vous avez maintenant activé le double chiffrement au repos sur votre disque managé.
