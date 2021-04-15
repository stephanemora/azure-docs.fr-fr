---
title: Comment gérer les interfaces réseau des machines virtuelles sur Azure Stack Edge Pro via le portail Azure
description: Découvrez comment gérer les interfaces réseau sur les machines virtuelles déployées sur votre GPU Azure Stack Edge Pro via le portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: 3e709b04b4eac60e6cc0ba3e53eb77583162dfef
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078891"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Utiliser le portail Azure pour gérer les interfaces réseau sur les machines virtuelles déployées sur votre GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Vous pouvez créer et gérer des machines virtuelles sur un appareil Azure Stack Edge à l’aide du portail Azure, des modèles, des cmdlets Azure PowerShell et via des scripts Azure CLI/Python. Cet article explique comment gérer les interfaces réseau d’une machine virtuelle qui s’exécute sur votre appareil Azure Stack Edge à l’aide du portail Azure. 

Lorsque vous créez une machine virtuelle, vous spécifiez une interface réseau virtuelle à créer. Vous souhaiterez peut-être ajouter une ou plusieurs interfaces réseau à la machine virtuelle après sa création. Vous pouvez également modifier les paramètres d’interface réseau par défaut d’une interface réseau existante.

Cet article explique comment ajouter une interface réseau à une machine virtuelle existante, modifier les paramètres existants tels que le type d’adresse IP (statique ou dynamique) et enfin supprimer ou détacher une interface existante. 

        
## <a name="about-network-interfaces-on-vms"></a>À propos des interfaces réseau sur les machines virtuelles

Une interface réseau permet à une machine virtuelle qui s’exécute sur votre appareil Azure Stack Edge Pro de communiquer avec les ressources Azure et locales. Lorsque vous activez un port pour le réseau de calcul sur votre appareil, un commutateur virtuel est créé sur cette interface réseau. Ce commutateur virtuel est ensuite utilisé pour déployer des charges de travail de calcul telles que des machines virtuelles ou des applications conteneurisées sur votre appareil. 

Votre appareil ne prend en charge qu’un seul commutateur virtuel, mais plusieurs interfaces réseau virtuelles. Chaque interface réseau sur votre machine virtuelle a une adresse IP statique ou dynamique qui lui est attribuée. Grâce aux adresses IP attribuées à plusieurs interfaces réseau sur votre machine virtuelle, certaines capacités sont activées sur votre machine virtuelle. Par exemple, votre machine virtuelle peut héberger plusieurs sites web ou services avec des adresses IP et des certificats SSL différents sur un seul serveur. Une machine virtuelle sur votre appareil peut servir d’appliance virtuelle réseau, comme un pare-feu ou un équilibreur de charge. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer à gérer des machines virtuelles sur votre appareil via le portail Azure, assurez-vous de ce qui suit :

1. Vous avez accès à un appareil GPU Azure Stack Edge Pro activé. Vous avez activé une interface réseau pour le calcul sur votre appareil. Cette action crée un commutateur virtuel sur cette interface réseau sur votre machine virtuelle. 
    1. Dans l’interface utilisateur locale de votre appareil, accédez à **Calcul**. Sélectionnez l’interface réseau que vous utiliserez pour créer un commutateur virtuel.

        > [!IMPORTANT] 
        > Vous ne pouvez configurer qu’un seul port pour le calcul.

    1. Activez le calcul sur l’interface réseau. Le GPU Azure Stack Edge Pro crée et gère un commutateur virtuel correspondant à cette interface réseau.

1. Au moins une machine virtuelle est déployée sur votre appareil. Pour créer cette machine virtuelle, consultez les instructions fournies dans [Déployer une machine virtuelle sur Azure Stack Edge Pro via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Votre machine virtuelle être à l’état **Arrêté**. Pour arrêter votre machine virtuelle, accédez à **Machines virtuelles > Vue d’ensemble** et sélectionnez la machine virtuelle que vous souhaitez arrêter. Dans la page de propriétés de la machine virtuelle, sélectionnez **Arrêter**, puis sélectionnez **Oui** lorsque vous êtes invité à confirmer. Avant d’ajouter, de modifier ou de supprimer des interfaces réseau, vous devez arrêter la machine virtuelle.

    ![Arrêter la machine virtuelle dans la page de propriétés de la machine virtuelle](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Ajouter une interface réseau

Procédez comme suit pour ajouter une interface réseau à une machine virtuelle déployée sur votre appareil. 

1. Accédez à la machine virtuelle que vous avez arrêtée et rendez-vous sur la page **Propriétés de la machine virtuelle**. Sélectionnez **Mise en réseau**.
    
    ![Sélectionner Mise en réseau sur la page des propriétés de la machine virtuelle](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. Dans le panneau **Mise en réseau**, à partir de la barre de commandes, sélectionnez **+ Ajouter une interface réseau**.

    ![Sélectionner Ajouter une interface réseau](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. Dans le panneau **Ajouter une interface réseau**, entrez les paramètres suivants :

    
    |Column1  |Column2  |
    |---------|---------|
    |Nom     | Nom unique dans le groupe de ressources. Il est impossible de modifier le nom une fois que l’interface réseau a été créée. Pour gérer facilement plusieurs interfaces réseau, utilisez les suggestions fournies dans les [conventions d’affectation des noms](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming).     |
    |Réseau virtuel| Réseau virtuel associé au commutateur virtuel créé sur votre appareil lorsque vous avez activé le calcul sur l’interface réseau. Il n’existe qu’un seul réseau virtuel associé à votre appareil.         |         
    |Subnet     | Sous-réseau au sein du réseau virtuel sélectionné. Ce champ est automatiquement renseigné avec le sous-réseau associé à l’interface réseau sur laquelle vous avez activé le calcul.         |       
    |Attribution d’adresse IP   | Adresse IP statique ou dynamique pour votre interface réseau. L’adresse IP statique doit correspondre à une adresse IP disponible dans la plage de sous-réseau spécifiée. Choisissez dynamique si un serveur DHCP existe dans l’environnement.        | 

    ![Panneau Ajouter une interface réseau](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Vous verrez une notification indiquant que la création de l’interface réseau est en cours.

    ![Notification lors de la création de l’interface réseau](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Une fois que l’interface réseau a été créée, la liste des interfaces réseau est actualisée pour afficher l’interface nouvellement créée.

    ![Liste mise à jour des interfaces réseau](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Modifier une interface réseau

Procédez comme suit pour modifier une interface réseau associée à une machine virtuelle déployée sur votre appareil.

1. Accédez à la machine virtuelle que vous avez arrêtée et rendez-vous sur la page **Propriétés de la machine virtuelle**. Sélectionnez **Mise en réseau**.

1. Dans la liste des interfaces réseau, sélectionnez l’interface que vous souhaitez modifier. À l’extrême droite de l’interface réseau sélectionnée, sélectionnez l’icône de modification (crayon).  

    ![Sélectionner une interface réseau à modifier](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. Dans le panneau **Modifier l’interface réseau**, vous pouvez uniquement modifier l’attribution d’adresse IP de l’interface réseau. Le nom, le réseau virtuel et le sous-réseau associés à l’interface réseau ne peuvent pas être modifiés une fois qu’elle a été créée. Modifiez l’**attribution d’adresse IP** en statique et enregistrez les modifications.

    ![Modifier l’attribution d’adresse IP pour l’interface réseau](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. La liste de l’interface réseau est actualisée pour afficher l’interface réseau mise à jour.


## <a name="detach-a-network-interface"></a>Détacher une interface réseau

Procédez comme suit pour détacher ou supprimer une interface réseau associée à une machine virtuelle déployée sur votre appareil.

1. Accédez à la machine virtuelle que vous avez arrêtée et rendez-vous sur la page **Propriétés de la machine virtuelle**. Sélectionnez **Mise en réseau**.

1. Dans la liste des interfaces réseau, sélectionnez l’interface que vous souhaitez modifier. À l’extrême droite de l’interface réseau sélectionnée, sélectionnez l’icône de détachement (déconnecter).  

    ![Sélectionner une interface réseau à détacher](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Une fois que l’interface est complètement détachée, la liste des interfaces réseau est actualisée pour afficher les interfaces restantes.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro, consultez [Déployer des machines virtuelles via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
