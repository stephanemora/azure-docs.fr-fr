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
ms.openlocfilehash: b8073240bdda38757a5e4feee66c9f54746966c4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632046"
---
### <a name="portal"></a>Portail

La configuration des clés gérées par le client pour vos disques vous oblige à créer des ressources dans un ordre particulier si vous les utilisez pour la première fois. Tout d’abord, vous devez créer et configurer un Azure Key Vault.

#### <a name="setting-up-your-azure-key-vault"></a>Configurer votre Azure Key Vault

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Recherchez et sélectionnez **Coffre de clés**.

    [ ![sse-key-vault-portal-search.png](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search.png)](media/virtual-machines-disk-encryption-portal/sse-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Pour que le déploiement aboutisse, votre coffre de clés Azure, le jeu de chiffrement de disque, les machines virtuelles, les disques et les captures instantanées doivent tous se trouver dans la même région et le même abonnement.

1. Sélectionnez **+Ajouter** pour créer un coffre de clés Key Vault.
1. Créez un groupe de ressources.
1. Entrez un nom de coffre de clés, sélectionnez une région, puis sélectionnez un niveau tarifaire.
1. Sélectionnez **Vérifier + créer**, vérifiez vos choix, puis sélectionnez **Créer**.

    ![Capture d’écran de l’expérience de création Azure Key Vault. Indication des valeurs spécifiques que vous créez](media/virtual-machines-disk-encryption-portal/sse-create-a-key-vault.png)

1. Une fois que votre coffre de clés a terminé le déploiement, sélectionnez-le.
1. Sous **Paramètres**, sélectionnez **Clés**.
1. Sélectionnez **Générer/Importer**.

    ![Capture d’écran du volet Paramètres des ressources Key Vault. Affiche le bouton Générer/importer dans les paramètres.](media/virtual-machines-disk-encryption-portal/sse-key-vault-generate-settings.png)

1. Laissez **Type de clé** défini sur **RSA** et **Taille de clé RSA** défini sur **2048**.
1. Renseignez les autres sélections comme vous le souhaitez, puis sélectionnez **Créer**.

    ![Capture d’écran du panneau Créer une clé qui apparaît une fois que le bouton Générer/importer est sélectionné](media/virtual-machines-disk-encryption-portal/sse-create-a-key-generate.png)

#### <a name="setting-up-your-disk-encryption-set"></a>Configuration de votre jeu de chiffrement de disque

1. Recherchez **Ensembles de chiffrement de disque** et sélectionnez-le.
1. Ouvrez le panneau **Ensembles de chiffrement de disque** et sélectionnez **+ Ajouter**.

    ![Capture d’écran de l’écran principal du portail de chiffrement de disque. Mise en surbrillance du bouton Ajouter](media/virtual-machines-disk-encryption-portal/sse-create-disk-encryption-set.png)

1. Sélectionnez votre groupe de ressources, attribuez un nom à votre jeu de chiffrement et sélectionnez la même région que votre coffre de clés.
1. Sélectionnez **Coffre de clés et clé**.
1. Sélectionnez le coffre de clés et la clé que vous avez créés précédemment, ainsi que la version.
1. Appuyez sur **Sélectionner**.
1. Sélectionnez **Vérifier + créer**, puis **Créer**.

    ![Capture d’écran du panneau de création du chiffrement de disque. Indique l’abonnement, le groupe de ressources, le nom du jeu de chiffrement de disque, la région et le coffre de clés + sélecteur de clé.](media/virtual-machines-disk-encryption-portal/sse-disk-enc-set-blade-key.png)

1. Ouvrez le jeu de chiffrement de disque une fois qu’il est créé et sélectionnez l’alerte qui s’affiche.

    ![Capture d’écran de la fenêtre contextuelle d’alerte : « Pour associer un disque, une image ou un instantané à ce jeu de chiffrement de disque, vous devez octroyer des autorisations au coffre de clés. » Sélectionnez cette alerte pour continuer](media/virtual-machines-disk-encryption-portal/sse-disk-enc-alert-fix.png)

Deux notifications doivent s’afficher et s’effectuer correctement. Cela vous permettra d’utiliser le jeu de chiffrement de disque avec votre coffre de clés.

![Capture d’écran de l’autorisation et de l’attribution de rôle réussie pour votre coffre de clés.](media/virtual-machines-disk-encryption-portal/disk-enc-notification-success.png)

#### <a name="deploy-a-vm"></a>Déployer une machine virtuelle

Maintenant que vous avez créé et configuré votre coffre de clés et le jeu de chiffrement de disque, vous pouvez déployer une machine virtuelle à l’aide du chiffrement.
Le processus de déploiement de la machine virtuelle est similaire au processus de déploiement standard, les seules différences sont que vous devez déployer la machine virtuelle dans la même région que vos autres ressources et que vous choisissez d’utiliser une clé gérée par le client.

1. Recherchez **Machines virtuelles** et sélectionnez **+ Ajouter** pour créer une machine virtuelle.
1. Sous l’onglet **De base**, sélectionnez la même région que votre jeu de chiffrement de disque et qu’Azure Key Vault.
1. Renseignez les autres valeurs de l’onglet **De base** comme vous le souhaitez.

    ![Capture d’écran de l’expérience de création de machines virtuelles, dont la valeur de région est mise en surbrillance.](media/virtual-machines-disk-encryption-portal/sse-create-a-vm-region.png)

1. Sous l’onglet **Disques**, sélectionnez **Chiffrement au repos avec une clé gérée par le client**.
1. Sélectionnez votre jeu de chiffrement de disque dans la liste déroulante **Jeu de chiffrement de disque**.
1. Effectuez les sélections restantes comme vous le souhaitez.

    ![Capture d’écran de l’expérience de création de machines virtuelles, dans le panneau Disques. Avec la liste déroulante Jeu de chiffrement de disque mise en surbrillance.](media/virtual-machines-disk-encryption-portal/sse-create-vm-select-cmk-encryption-set.png)

#### <a name="enable-on-an-existing-disk"></a>Activer sur un disque existant

> [!CAUTION]
> L’activation du chiffrement de disque sur les disques attachés à une machine virtuelle nécessite l’arrêt de celle-ci.
    
1. Accédez à une machine virtuelle qui se trouve dans la même région que l’un de vos jeux de chiffrement de disque.
1. Ouvrez la machine virtuelle et sélectionnez **Arrêter**.

    ![Capture d’écran de la superposition principale pour votre exemple de machine virtuelle. Avec le bouton Arrêter mis en surbrillance](media/virtual-machines-disk-encryption-portal/sse-stop-VM-to-encrypt-disk.png)

1. Une fois l’arrêt de la machine virtuelle terminé, sélectionnez **Disques** puis sélectionnez le disque que vous souhaitez chiffrer.

    ![Capture d’écran de votre exemple de machine virtuelle, avec le panneau Disques ouvert. Le disque du système d’exploitation est mis en surbrillance, comme exemple de disque à sélectionner.](media/virtual-machines-disk-encryption-portal/sse-existing-disk-select.png)

1. Sélectionnez **Chiffrement**, **Chiffrement au repos avec une clé gérée par le client**, puis sélectionnez votre jeu de chiffrement de disque dans la liste déroulante.
1. Sélectionnez **Enregistrer**.

    ![Capture d’écran de votre exemple de disque de système d’exploitation. Le panneau Chiffrement est ouvert, le chiffrement au repos avec une clé gérée par le client est sélectionné, ainsi que votre exemple Azure Key Vault. Après avoir effectué ces sélections, le bouton Enregistrer est sélectionné.](media/virtual-machines-disk-encryption-portal/sse-encrypt-existing-disk-customer-managed-key.png)

1. Répétez ce processus pour tous les autres disques attachés à la machine virtuelle que vous souhaitez chiffrer.
1. S’il n’y a pas d’autres disques attachés que vous souhaitez chiffrer lorsque vos disques ont fini de basculer vers les clés gérées par le client, vous pouvez démarrer votre machine virtuelle.
