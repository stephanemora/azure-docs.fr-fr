---
title: Comment gérer les interfaces réseau des machines virtuelles sur Azure Stack Edge Pro via le portail Azure
description: Découvrez comment gérer les interfaces réseau sur les machines virtuelles déployées sur votre GPU Azure Stack Edge Pro via le portail Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 27927f1e5f31243d4d2dcbfd9ea1f4da09b8b544
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532037"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Utiliser le portail Azure pour gérer les interfaces réseau sur les machines virtuelles déployées sur votre GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Vous pouvez créer et gérer des machines virtuelles sur un appareil Azure Stack Edge à l’aide du portail Azure, des modèles, des cmdlets Azure PowerShell et via des scripts Azure CLI/Python. Cet article explique comment gérer les interfaces réseau d’une machine virtuelle qui s’exécute sur votre appareil Azure Stack Edge à l’aide du portail Azure.

Lorsque vous créez une machine virtuelle, vous spécifiez une interface réseau virtuelle à créer. Vous souhaiterez peut-être ajouter une ou plusieurs interfaces réseau à la machine virtuelle après sa création. Vous pouvez également modifier les paramètres d’interface réseau par défaut d’une interface réseau existante.

Cet article explique comment ajouter une interface réseau à une machine virtuelle existante, modifier les paramètres existants tels que le type d’adresse IP (statique/dynamique) et détacher ou supprimer une interface existante.

        
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

1. Au moins une machine virtuelle est déployée sur votre appareil. Pour créer cette machine virtuelle, consultez les instructions fournies dans [Déployer une machine virtuelle sur Azure Stack Edge Pro via le Portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. Votre machine virtuelle être à l’état **Arrêté**. Pour arrêter votre machine virtuelle, accédez à **Machines virtuelles** et sélectionnez la machine virtuelle à arrêter. Dans la page **Détails** de la machine virtuelle, sélectionnez **Arrêter**, puis sélectionnez **Oui** lorsque vous êtes invité à confirmer l’opération. Avant d’ajouter, de modifier ou de supprimer des interfaces réseau, vous devez arrêter la machine virtuelle.

    ![Capture d’écran montrant l’invite de confirmation de l’arrêt d’une machine virtuelle dans Azure Stack Edge.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Ajouter une interface réseau

Procédez comme suit pour ajouter une interface réseau à une machine virtuelle déployée sur votre appareil.<!--There's no obvious way to add a new NIC to a VM or to an Edge resource group in the portal. To update these procedures, I need to create my own test VM, which I can start and stop, create a new NIC for, detach a NIC from the stopped VM, etc.-->

1. Accédez à la machine virtuelle que vous avez arrêtée et sélectionnez **Réseau**.
    
    ![Capture d’écran montrant l’onglet Détails pour une machine virtuelle. L’étiquette Réseau, qui ouvre le panneau Réseau pour la machine virtuelle, est mise en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. Dans le panneau **Mise en réseau**, à partir de la barre de commandes, sélectionnez **+ Ajouter une interface réseau**.

    ![Capture d’écran montrant le panneau Réseau pour une machine virtuelle. Le bouton « + Ajouter une interface réseau » est mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. Dans le panneau **Ajouter une interface réseau**, entrez les paramètres suivants :

    |Champ    |Description  |
    |---------|-------------|
    |Name     | Nom unique dans le groupe de ressources Edge. Il est impossible de modifier le nom une fois que l’interface réseau a été créée. Pour gérer facilement plusieurs interfaces réseau, utilisez les suggestions fournies dans les [conventions d’affectation des noms](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming).     |
    |Sélectionner un groupe de ressources Edge |Sélectionnez le groupe de ressources Edge auquel ajouter l’interface réseau.|   
    |Réseau virtuel| Réseau virtuel associé au commutateur virtuel créé sur votre appareil lorsque vous avez activé le calcul sur l’interface réseau. Il n’existe qu’un seul réseau virtuel associé à votre appareil.         |
    |Subnet     | Sous-réseau au sein du réseau virtuel sélectionné. Ce champ est automatiquement renseigné avec le sous-réseau associé à l’interface réseau sur laquelle vous avez activé le calcul.         |
    |Affectation d’adresses IP   | Adresse IP statique ou dynamique pour votre interface réseau. L’adresse IP statique doit correspondre à une adresse IP disponible dans la plage de sous-réseau spécifiée. Choisissez dynamique si un serveur DHCP existe dans l’environnement.        |

    ![Capture d’écran montrant le panneau Ajouter une interface réseau pour une machine virtuelle. Le bouton Ajouter est mis en surbrillance.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Vous verrez une notification indiquant que la création de l’interface réseau est en cours.

    ![Capture d’écran montrant la notification indiquant qu’une interface réseau est en cours de création.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  Une fois que l’interface réseau a été créée, la liste des interfaces réseau est actualisée pour afficher l’interface nouvellement créée.

    ![Capture d’écran du panneau Réseau pour une machine virtuelle. L’entrée d’une machine virtuelle nouvellement créée est mise en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Modifier une interface réseau

Procédez comme suit pour modifier une interface réseau associée à une machine virtuelle déployée sur votre appareil.

1. Accédez à la machine virtuelle que vous avez arrêtée et sélectionnez **Réseau** dans le panneau **Détails** de la machine virtuelle.

1. Dans la liste des interfaces réseau, sélectionnez l’interface que vous souhaitez modifier. À l’extrême droite de l’interface réseau sélectionnée, sélectionnez l’icône de modification (crayon).  

    ![Capture d’écran montrant le panneau Réseau pour une machine virtuelle. Le nom d’une interface réseau et l’icône de crayon de l’entrée sont mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. Dans le panneau **Modifier l’interface réseau**, vous pouvez uniquement modifier l’attribution d’adresse IP de l’interface réseau. Le nom, le groupe de ressources Edge, le réseau virtuel et le sous-réseau associés à l’interface réseau ne peuvent pas être modifiés une fois celle-ci créée. Changez l’**attribution d’adresse IP** en statique et enregistrez les modifications.

    ![Capture d’écran du panneau Modifier l’interface réseau pour une machine virtuelle. La zone attribution d’adresse IP et le bouton Enregistrer sont mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. La liste de l’interface réseau est actualisée pour afficher l’interface réseau mise à jour.


## <a name="detach-a-network-interface"></a>Détacher une interface réseau

Procédez comme suit pour détacher ou supprimer une interface réseau associée à une machine virtuelle déployée sur votre appareil.

1. Accédez à la machine virtuelle que vous avez arrêtée et sélectionnez **Réseau** dans le panneau **Détails** de la machine virtuelle.

1. Dans la liste des interfaces réseau, sélectionnez l’interface que vous souhaitez modifier. À l’extrême droite de l’interface réseau sélectionnée, sélectionnez l’icône de détachement (déconnecter).  

    ![Capture d’écran du panneau Réseau pour une machine virtuelle. Le nom d’une interface réseau et l’icône de détachement sont mis en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Un message s’affiche pour vous demander de confirmer le détachement de l’interface réseau. Sélectionnez **Oui**.

    ![Capture d’écran de la notification vous invitant à confirmer que vous souhaitez détacher une interface réseau d’une machine virtuelle.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-2.png)

    Une fois que l’interface est complètement détachée, la liste des interfaces réseau est actualisée pour afficher les interfaces restantes.


## <a name="delete-a-network-interface"></a>Supprimer une interface réseau

Effectuez les étapes suivantes pour supprimer une interface réseau qui n’est pas attachée à une machine virtuelle.

1. Accédez à **Machines virtuelles**, puis à la page **Ressources**. Sélectionnez **Mise en réseau**.
    
    ![Capture d’écran montrant l’onglet Réseau dans la page Ressources pour les machines virtuelles. Les étiquettes du volet Ressources et de l’onglet Réseau sont mises en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-1.png)

1. Dans le panneau **Ressources**, sélectionnez l’icône de suppression (corbeille) à côté de l’interface réseau à supprimer. L’icône de suppression est visible seulement pour les interfaces réseau qui ne sont pas attachées à une machine virtuelle.

    ![Capture d’écran montrant le panneau Réseau dans la page Ressources pour les machines virtuelles. L’icône de suppression pour une interface réseau non attachée est mise en évidence.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-2.png)

1. Un message s’affiche pour vous demander de confirmer la suppression de l’interface réseau. L’opération est définitive. Sélectionnez **Oui**.

    ![Capture d’écran de la notification vous invitant à confirmer la suppression d’une interface réseau sélectionnée.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/delete-nic-3.png)
  
    Une fois l’interface réseau supprimée, elle n’apparaît plus dans la liste.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro, consultez [Déployer des machines virtuelles via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
