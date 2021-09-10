---
title: Déployer des machines virtuelles sur votre appliance Azure Stack Edge Pro GPU via le portail Azure
description: Découvrez comment déployer des machines virtuelles sur votre appliance Azure Stack Edge Pro GPU via le portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 8089666388134e8443c515d86e91ae3ba5c32b58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532740"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Déployer des machines virtuelles sur Azure Stack Edge Pro avec GPU via le portail Azure

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Vous pouvez créer et gérer des machines virtuelles sur un appareil Azure Stack Edge Pro GPU à l’aide du portail Azure, de modèles et de cmdlets Azure PowerShell, ainsi que via des scripts Azure CLI ou Python. Cet article explique comment créer et gérer une machine virtuelle sur votre appareil Azure Stack Edge Pro GPU à l’aide du portail Azure.

> [!IMPORTANT] 
> Nous vous recommandons d’activer l’authentification multifacteur pour l’utilisateur qui gère les machines virtuelles déployées sur votre appareil à partir du cloud.
        
## <a name="vm-deployment-workflow"></a>Workflow du déploiement de machine virtuelle

Voici une synthèse globale du workflow de déploiement :

1. Activez une interface réseau pour le calcul sur votre appareil Azure Stack Edge. Cette étape crée un commutateur virtuel sur l’interface réseau spécifiée.
1. Activez la gestion cloud des machines virtuelles à partir du portail Azure.
1. Chargez un disque dur virtuel dans un compte Stockage Azure à l’aide de l’Explorateur de stockage Azure. 
1. Utilisez le disque dur virtuel chargé pour télécharger le disque dur virtuel sur l’appareil et l’utiliser pour créer une image de machine virtuelle. 
1. Utilisez les ressources créées durant les étapes précédentes :
    1. Image de machine virtuelle que vous avez créée.
    1. Commutateur virtuel associé à l’interface réseau sur laquelle vous avez activé le calcul.
    1. Sous-réseau associé au commutateur virtuel.

    Créez ou spécifiez les ressources suivantes en ligne :
    1. Nom de la machine virtuelle, choisissez une taille de machine virtuelle prise en charge, informations d’identification de connexion pour la machine virtuelle. 
    1. Créez des disques de données ou attachez des disques de données existants.
    1. Configurez une adresse IP statique ou dynamique pour la machine virtuelle. Si vous fournissez une adresse IP statique, choisissez à partir d’une adresse IP libre dans la plage de sous-réseau de l’interface réseau activée pour le calcul.

    Utilisez les ressources précédentes pour créer une machine virtuelle.

## <a name="prerequisites"></a>Prérequis

Avant de commencer à créer et à gérer des machines virtuelles sur votre appareil via le portail Azure, assurez-vous de ce qui suit :

1. Vous avez appliqué les paramètres réseau sur votre appareil Azure Stack Edge Pro comme décrit dans [Étape 1 : Configurer un appareil Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-device).

    1. Vous avez activé une interface réseau pour le calcul. Cette adresse IP d’interface réseau sert à créer un commutateur virtuel pour le déploiement de la machine virtuelle. Dans l’interface utilisateur locale de votre appareil, accédez à **Calcul**. Sélectionnez l’interface réseau que vous allez utiliser pour créer un commutateur virtuel.

        > [!IMPORTANT] 
        > Vous ne pouvez configurer qu’un seul port pour le calcul.

    1. Activez le calcul sur l’interface réseau. Le GPU Azure Stack Edge Pro crée et gère un commutateur virtuel correspondant à cette interface réseau.

1. Vous avez accès à un disque dur virtuel Windows ou Linux que vous utiliserez pour créer l’image de la machine virtuelle que vous souhaitez créer.

## <a name="deploy-a-vm"></a>Déployer une machine virtuelle

Pour créer une machine virtuelle sur votre appareil Azure Stack Edge Pro GPU, procédez comme suit.

### <a name="add-a-vm-image"></a>Ajouter une image de machine virtuelle

1. Chargez de disque dur virtuel sur un compte de stockage Azure. Suivez les étapes décrites dans [Chargement à l’aide de l’Explorateur Stockage](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload).

   Pour plus d’informations sur la préparation du disque dur virtuel, consultez [Préparer une image généralisée à partir d’un disque dur virtuel Windows](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md).

   [Dépannez les chargements d’images de machine virtuelle](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md).

1. Dans le portail Azure, accédez à la ressource Azure Stack Edge de votre appareil. Ensuite, accédez à **Services Edge** > **Machines virtuelles**.

    ![Capture d’écran montrant le volet Vue d’ensemble d’un appareil Azure Stack Edge, avec l’option Machines virtuelles, sous Services Edge, mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Sur la page **Vue d’ensemble**. Sélectionnez **Activer** pour activer la gestion cloud des machines virtuelles. 

    ![Capture d’écran du volet Vue d’ensemble de la vue Machines virtuelles pour un appareil Azure Stack Edge. Le bouton Activer, qui permet d’activer la gestion cloud des machines virtuelles, est mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. La première étape consiste à ajouter une image de machine virtuelle. Au cours de l’étape précédente, vous avez chargé un disque dur virtuel dans le compte de stockage. Vous allez utiliser ce disque dur virtuel pour créer une image de machine virtuelle.

    Sélectionnez **+ Ajouter une image** pour télécharger le disque dur virtuel à partir du compte de stockage et l’ajouter à l’appareil. Le processus de téléchargement prend plusieurs minutes selon la taille du disque dur virtuel et de la bande passante Internet disponible pour le téléchargement. 

    ![Capture d’écran du volet Vue d’ensemble pour Machines virtuelles, avec le bouton + Ajouter une image mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. Dans le volet **Ajouter une image**, renseignez les champs suivants. Sélectionnez ensuite **Ajouter**.

    |Champ  |Description  |
    |---------|---------|
    |Télécharger à partir d’un objet blob de stockage    |Accédez à l’emplacement de l’objet blob de stockage dans le compte de stockage où vous avez chargé le disque dur virtuel.         |
    |Télécharger vers    | Défini automatiquement sur l’appareil actuel sur lequel vous déployez la machine virtuelle.        |
    |Groupe de ressources Edge  |Sélectionnez le groupe de ressources auquel ajouter l’image. |
    |Enregistrer l’image sous      | Nom de l’image de machine virtuelle que vous créez à partir du disque dur virtuel chargé dans le compte de stockage.        |
    |Type de système d’exploitation     |Sélectionnez Windows ou Linux en tant que système d’exploitation du disque dur virtuel que utiliserez pour créer l’image de machine virtuelle.         |
   

    ![Capture d’écran montrant la page Ajouter une image pour une machine virtuelle, avec le bouton Ajouter mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. Le disque dur virtuel est téléchargé et l’image de machine virtuelle est créée. Cette opération prend plusieurs minutes. Une notification s’affiche une fois l’image de machine virtuelle créée.<!--There's a fleeting notification that image creation is in progress, but I didn't see any notification that image creation completed successfully.-->

    ![Capture d’écran montrant le volet Vue d’ensemble pour Machines virtuelles sur un appareil Azure Stack Edge. Dans le volet Images, une machine virtuelle nouvellement créée est mise en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. Une fois créée, l’image de machine virtuelle est ajoutée à la liste des images dans le volet **Images**.

    ![Capture d’écran montrant le volet Images dans la vue Machines virtuelles d’un appareil Azure Stack Edge. L’entrée d’une image de machine virtuelle est mise en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    Le volet **Déploiements** se met à jour pour indiquer l’état du déploiement.

    ![Capture d’écran montrant le volet Déploiements dans la vue Machines virtuelles d’un appareil Azure Stack Edge. L’entrée d’une machine virtuelle déployée est mise en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    L’image nouvellement ajoutée s’affiche également dans la page **Vue d’ensemble**.

    ![Capture d’écran montrant le volet Vue d’ensemble pour Machines virtuelles sur un appareil Azure Stack Edge. Une image de machine virtuelle nouvellement ajoutée est mise en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Ajouter une machine virtuelle

Suivez la procédure ci-dessous pour créer une machine virtuelle après avoir créé une image de machine virtuelle.

1. Dans la page **Vue d’ensemble** pour **Machines virtuelles**, sélectionnez **+ Ajouter une machine virtuelle**.

    ![Capture d’écran montrant le volet Vue d’ensemble pour Machines virtuelles sur un appareil Azure Stack Edge. Le bouton + Ajouter une machine virtuelle est mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Sous l’onglet **Informations de base**, entrez les paramètres suivants.

    |Paramètre |Description  |
    |---------|---------|
    |Nom de la machine virtuelle     | Entrez le nom de la nouvelle machine virtuelle.        |
    |Groupe de ressources Edge     | Créez un groupe de ressources pour toutes les ressources associées à la machine virtuelle.        |
    |Image     | Effectuez une sélection parmi les images de machine virtuelle disponibles sur l’appareil.        |
    |Taille     | Effectuez une sélection parmi les [Tailles de machine virtuelle prises en charge](azure-stack-edge-gpu-virtual-machine-sizes.md).<br>Pour une machine virtuelle avec GPU, sélectionnez une [taille de machine virtuelle de la série NCasT4-v3](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview). |
    |Nom d’utilisateur     | Utilisez le nom d’utilisateur par défaut, **azureuser**, pour la connexion de l’administrateur à la machine virtuelle.        |
    |Type d'authentification    | Sélectionnez une clé publique SSH ou un mot de passe défini par l’utilisateur.       |
    |Clé publique SSH | Affiché lorsque vous sélectionnez le type d’authentification **Clé publique SSH**. Collez-y la clé publique SSH. |
    |Mot de passe     | Affiché lorsque vous sélectionnez le type d’authentification **Mot de passe**. Entrez un mot de passe de connexion à la machine virtuelle. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux [exigences de complexité ](../virtual-machines/windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-)définies. |
    |Confirmer le mot de passe    | Entrez de nouveau le mot de passe.        |

    ![Capture d’écran montrant l’onglet Informations de base dans l’Assistant Ajouter une machine virtuelle pour Azure Stack Edge. L’onglet Informations de base et le bouton Suivant : Disques sont mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Sélectionnez **Suivant : Disques**.

1. Dans l’onglet **Disques**, vous allez attacher des disques à votre machine virtuelle. 
    
    1. Vous pouvez choisir entre **Créer et attacher un disque** ou **Attacher un disque existant**.

        ![Capture d’écran montrant l’onglet Disques dans l’Assistant Ajouter une machine virtuelle pour Azure Stack Edge. L’option Créer et attacher un disque est mise en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Sélectionnez **Créer et attacher un disque**. Dans le volet **Créer un disque**, indiquez le nom du disque et sa taille en Gio.

        ![Capture d’écran montrant l’écran « Créer un disque » dans « Ajouter une machine virtuelle » pour Azure Stack Edge. Le bouton OK est mis en surbrillance.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1. Répétez les étapes précédentes pour ajouter d’autres disques. Une fois les disques créés, ils s’affichent sous l’onglet **Disques**. Sélectionnez **Suivant : Mise en réseau**.

1. Sous l’onglet **Mise en réseau**, vous allez configurer la connectivité réseau de votre machine virtuelle.

    |Paramètre  |Description |
    |---------|---------|
    |Réseau virtuel    | Dans la liste déroulante, sélectionnez le commutateur virtuel créé sur votre appareil Azure Stack Edge lorsque vous avez activé le calcul sur l’interface réseau.    |
    |Subnet     | Ce champ est automatiquement renseigné avec le sous-réseau associé à l’interface réseau sur laquelle vous avez activé le calcul.         |
    |Adresse IP     | Indiquez une adresse IP statique ou dynamique pour votre machine virtuelle. L’adresse IP statique doit correspondre à une adresse IP disponible dans la plage de sous-réseau spécifiée.        |

    ![Capture d’écran de l’onglet Mise en réseau dans « Ajouter une machine virtuelle » pour Azure Stack Edge. L’onglet Mise en réseau et le bouton Suivant : Avancé sont mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Sélectionnez **Suivant : Avancé**. Dans l’onglet **Avancé**, vous pouvez sélectionner une extension à installer pendant le déploiement de la machine virtuelle, et vous pouvez spécifier un script `cloud-init` pour personnaliser votre machine virtuelle.

1. Si vous souhaitez installer une extension sur votre machine virtuelle lorsque vous la créez, choisissez **Sélectionner une extension à installer**. Sélectionnez ensuite l’extension dans l’écran **Ajouter une extension**.

    Pour connaître les étapes détaillées de l’installation d’une extension GPU pendant le déploiement d’une machine virtuelle, consultez [Déployer des machines virtuelles avec GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).

    ![Illustration de deux étapes pour ajouter une extension GPU à l’onglet Avancé dans « Ajouter une machine virtuelle ». Les options permettant de sélectionner et d’ajouter une extension sont mises en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-extension-01.png)

1. Si vous souhaitez utiliser l’utilitaire `cloud-init` pour personnaliser la nouvelle machine virtuelle lors de son premier démarrage, dans l’onglet **Avancé**, collez votre script `cloud-init` dans la zone **Données personnalisées** sous **Données personnalisées et cloud-init**. 

    Pour plus d’informations sur l’utilisation de `cloud-init`, consultez [Présentation de cloud-init](../virtual-machines/linux/tutorial-automate-vm-deployment.md#cloud-init-overview).

    ![Capture d’écran montrant l’onglet Avancé de l’Assistant Créer une nouvelle machine virtuelle, avec un script cloud-init dans la zone Données personnalisées mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-advanced-tab-with-cloud-init-script.png)

    Sélectionnez **Suivant : Vérifier + créer**.

1. Sur l’onglet **Vérifier + Créer**, passez en revue les spécifications de la machine virtuelle. Sélectionnez ensuite **Créer**.

    ![Capture d’écran montrant l’onglet Révision + créer de l’Assistant « Ajouter une machine virtuelle » dans Azure Stack Edge. L’onglet Révision + créer est mis en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. La création de la machine virtuelle démarre et peut prendre jusqu’à 20 minutes. Vous pouvez accéder à **Déploiements** pour suivre la création de la machine virtuelle.

    ![Capture d’écran montrant le volet Déploiements dans la vue Machines virtuelles d’un appareil Azure Stack Edge. L’entrée d’une machine virtuelle déployée est mise en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

1. Une fois la machine virtuelle créée, vous verrez votre nouvelle machine virtuelle dans le volet **Vue d’ensemble**.

    ![Capture d’écran montrant le volet Vue d’ensemble pour Machines virtuelles sur un appareil Azure Stack Edge. Une nouvelle machine virtuelle est mise en évidence dans la liste.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Sélectionnez la machine virtuelle que vous venez de créer pour accéder à **Machines virtuelles**.

    ![Capture d’écran montrant le volet Machines virtuelles d’un appareil Azure Stack Edge. L’étiquette Machines virtuelles et une entrée de machine virtuelle sont mises en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    Sélectionnez la machine virtuelle pour afficher les détails correspondants.

    ![Capture d’écran montrant l’onglet Détails du volet Vue d’ensemble d’une machine virtuelle dans Azure Stack Edge. L’étiquette Vue d’ensemble et l’adresse IP dans Mise en réseau sont mises en évidence.](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

    Vous allez utiliser l’adresse IP de l’interface réseau pour vous connecter à la machine virtuelle.

## <a name="connect-to-a-vm"></a>Se connecter à une machine virtuelle

Selon que vous avez créé une machine virtuelle Linux ou Windows, les étapes de connexion peuvent être différentes. Vous ne pouvez pas vous connecter aux machines virtuelles déployées sur votre appareil via le portail Azure. Procédez comme suit pour connecter votre machine virtuelle Linux ou Windows.

### <a name="connect-to-a-linux-vm"></a>Se connecter à une machine virtuelle Linux

Suivez ces étapes pour vous connecter à une machine virtuelle Linux.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-a-windows-vm"></a>Se connecter à une machine virtuelle Windows

Suivez ces étapes pour vous connecter à une machine virtuelle Windows.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Étapes suivantes

- [Déployer une machine virtuelle avec GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)
- [Résoudre les problèmes de déploiement de machines virtuelles](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Superviser l’activité des machines virtuelles sur votre appareil](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Superviser l’utilisation de l’UC et de la mémoire sur une machine virtuelle](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)

