---
title: 'Démarrage rapide : Activer l’Autogestion Azure pour machines virtuelles dans le portail Azure'
description: Découvrez comment activer rapidement l’Autogestion pour une machine virtuelle nouvelle ou existante dans le portail Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: jushiman
ms.openlocfilehash: 6e0e582ed37230ba3f379f193a229cfec06f066c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648031"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Démarrage rapide : Activer l’Autogestion Azure pour machines virtuelles dans le portail Azure

Utilisez l’Autogestion Azure dans le portail Azure afin d’activer la gestion automatique sur une machine virtuelle nouvelle ou existante.


## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, [créez un compte](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) avant de commencer.

> [!NOTE]
> Les comptes associés à un essai gratuit n’ont pas accès aux machines virtuelles utilisées dans ce tutoriel. Veuillez passer à un abonnement avec paiement à l’utilisation.

> [!IMPORTANT]
> Vous devez disposer du rôle **Contributeur** sur le groupe de ressources contenant vos machines virtuelles pour activer Automanage à l’aide d’un compte Automanage existant. Si vous activez Automanage avec un nouveau compte Automanage, vous devez disposer des autorisations suivantes : Rôle de **Propriétaire**, ou rôle de **Contributeur** associé au rôle d’**Administrateur de l’accès utilisateur** sur votre abonnement.


## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://aka.ms/AutomanagePortal-Ignite21).

## <a name="enable-automanage-for-a-single-vm"></a>Activer Automanage pour une seule machine virtuelle

1. Recherchez la machine virtuelle pour laquelle vous souhaitez effectuer l’activation.

2. Cliquez sur l’entrée **Automanage (préversion)** dans la table des matières sous **Opérations**.

3. Sélectionnez **Prise en main**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-GetStarted.png" alt-text="Démarrer pour une seule machine virtuelle.":::

4. Choisissez vos paramètres Automanage (environnement, préférences, compte Automanage), puis cliquez sur **Activer**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMManage-Enable.png" alt-text="Activer sur une seule machine virtuelle.":::

## <a name="enable-automanage-for-multiple-vms"></a>Activer Automanage pour plusieurs machines virtuelles

1. Dans la barre de recherche, recherchez et sélectionnez **Automanage - Azure machine best practices** (Automanage - Bonnes pratiques pour les machines virtuelles Azure).

2. Sélectionnez l’option **Activer sur la machine virtuelle existante**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Activation sur la machine virtuelle existante":::

3. Dans le panneau **Sélectionner les machines** :
    1. Filtrez la liste des machines virtuelles selon votre **abonnement** et votre **groupe de ressources**.
    1. Cochez la case des machines virtuelles que vous souhaitez intégrer.
    1. Cliquez sur le bouton **Sélectionner**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Sélectionnez une machine virtuelle existante dans la liste des machines virtuelles disponibles.":::

4. Sous **Profil de configuration**, cliquez sur **Parcourir et modifier les profils et les préférences**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Parcourir et modifier les profils et les préférences":::

5. Dans le panneau **Sélectionner le profil de configuration + préférences** :
    1. Sélectionnez un profil sur la gauche : *Dev/Test* à des fins de test, *Prod* pour la production.
    1. Cliquez sur le bouton **Sélectionner**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Parcourir le profil de configuration de production":::

6. Cliquez sur le bouton **Activer**.


## <a name="enable-automanage-for-a-new-vm"></a>Activer Automanage pour une nouvelle machine virtuelle

Connectez-vous au portail Azure à partir d'[ici](https://aka.ms/AutomanagePortal-Ignite21) pour créer une nouvelle machine virtuelle et activer Automanage.

1. Choisissez **Créer une ressource** en haut à gauche du Portail Azure.

2. Dans la zone de recherche située au-dessus de la liste des ressources de la Place de marché Azure, recherchez et sélectionnez l'image que vous souhaitez utiliser, puis choisissez **Créer**.

> [!NOTE]
> Vérifiez les [distributions Linux](automanage-linux.md#supported-linux-distributions-and-versions) et [versions de Windows Server](automanage-windows-server.md#supported-windows-server-versions) prises en charge par Automanage.

3. Indiquez sous l’onglet **De base** les détails de votre machine virtuelle.

> [!NOTE]
> Vérifiez les [régions prises en charge](automanage-virtual-machines#supported-regions) par Automanage.

4. Accédez à l’onglet **Gestion**, puis choisissez votre **environnement Automanage**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\VMCreate-Management-Tab.png" alt-text="Activer Automanage sous l’onglet Gestion.":::

5. Conservez les valeurs par défaut restantes, puis sélectionnez le bouton **Vérifier + créer** en bas de la page.

6. Lorsque vous voyez le message indiquant que la validation a réussi, sélectionnez **Créer**.

## <a name="disable-automanage-for-vms"></a>Désactiver l’Autogestion pour machines virtuelles

Vous pouvez cesser rapidement d’utiliser l’Autogestion Azure pour machines virtuelles en désactivant la gestion automatique.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Désactivation de l’Autogestion sur une machine virtuelle":::

1. Accédez à la page **Autogestion – Bonnes pratiques pour les machines virtuelles** qui liste toutes vos machines virtuelles qui sont gérées automatiquement.
1. Cochez la case des machines virtuelles que vous souhaitez désactiver.
1. Cliquez sur le bouton **Désactiver la gestion automatique**.
1. Lisez attentivement le message dans la fenêtre contextuelle qui s’affiche avant d’accepter de **Désactiver**.


## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous avez créé un nouveau groupe de ressources pour essayer l’Autogestion Azure pour machines virtuelles, et que vous n’en avez plus besoin, vous pouvez le supprimer. La suppression du groupe supprime également la machine virtuelle, ainsi que toutes les autres ressources se trouvant dans le groupe.

L’Autogestion Azure crée des groupes de ressources par défaut dans lesquels stocker les ressources. Vérifiez les groupes de ressources qui suivent la convention de nommage « DefaultResourceGroupRegionName » ou « AzureBackupRGRegionName » afin de nettoyer toutes les ressources.

1. Sélectionnez le **groupe de ressources**.
1. Dans la page du groupe de ressources, sélectionnez **Supprimer**.
1. Lorsque vous y êtes invité, confirmez le nom du groupe de ressources, puis sélectionnez **Supprimer**.


## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez activé l’Autogestion Azure pour machines virtuelles.

Découvrez comment vous pouvez créer et appliquer des préférences personnalisées lors de l’activation de l’Autogestion sur votre machine virtuelle.

> [!div class="nextstepaction"]
> [Azure Automanage pour les machines virtuelles - Profil de configuration personnalisé](virtual-machines-custom-preferences.md)
