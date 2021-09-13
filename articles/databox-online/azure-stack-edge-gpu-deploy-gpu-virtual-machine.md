---
title: Déployer des machines virtuelles GPU sur votre appareil Azure Stack Edge Pro GPU
description: Explique comment créer et déployer des machines virtuelles GPU sur un appareil Azure Stack Edge Pro GPU en utilisant le portail Azure ou des modèles.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 958c87a78551555d2994c37e2b72c75cb989a834
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532267"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Déployer des machines virtuelles GPU sur votre appareil Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Cet article explique comment créer une machine virtuelle GPU en utilisant le portail Azure ou les modèles Azure Resource Manager.

Utilisez le portail Azure pour déployer rapidement une machine virtuelle GPU unique. Vous pouvez installer l’extension GPU pendant ou après la création de la machine virtuelle. Ou utilisez des modèles Azure Resource Manager pour déployer et gérer efficacement plusieurs machines virtuelles GPU.

## <a name="create-gpu-vms"></a>Créer des machines virtuelles GPU

Vous pouvez déployer une machine virtuelle GPU par le biais du portail ou en utilisant des modèles Azure Resource Manager.

Pour obtenir la liste des systèmes d’exploitation, des pilotes et des tailles de machine virtuelle pris en charge pour les machines virtuelles GPU, consultez [Que sont les machines virtuelles GPU ?](azure-stack-edge-gpu-overview-gpu-virtual-machines.md). Pour obtenir des informations sur le déploiement, consultez [Machines virtuelles GPU et Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes).


> [!IMPORTANT]
> Si votre appareil doit exécuter Kubernetes, ne configurez pas Kubernetes avant de déployer vos machines virtuelles GPU. Si vous configurez Kubernetes en premier, Kubernetes revendiquera toutes les ressources GPU disponibles, provoquant l’échec de la création des machines virtuelles GPU. Pour plus d’informations sur le déploiement de Kubernetes sur des appareils avec un ou deux GPU, consultez [Machines virtuelles GPU et Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes).

### <a name="portal"></a>[Portail](#tab/portal)

Pour déployer des machines virtuelles GPU sur votre appareil par le biais du portail Azure, effectuez ces étapes :

1. Pour créer des machines virtuelles GPU, effectuez toutes les étapes décrites dans [Déployer des machines virtuelles sur votre appareil Azure Stack Edge via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md), en respectant la configuration requise suivante :

    - Sous l’onglet **Informations de base**, sélectionnez une [taille de machine virtuelle de la série NCasT4-v3](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

       ![Capture d’écran de l’onglet Informations de base de la fenêtre Ajouter une machine virtuelle dans Azure Stack Edge. L’option Taille, avec une taille de machine virtuelle prise en charge pour les machines virtuelles GPU, est mise en évidence.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/basics-vm-size-for-gpu.png)

    - Pour installer l’extension GPU pendant le déploiement, sous l’onglet **Avancé**, choisissez **Sélectionner une extension à installer**. Sélectionnez ensuite l’extension GPU à installer. Les extensions GPU sont uniquement disponibles pour les machines virtuelles ayant une [taille de machine virtuelle de la série NCasT4-v3](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).
        
        > [!NOTE]
        > Si vous utilisez une image Red Hat, veillez à installer l’extension GPU après le déploiement de la machine virtuelle. Effectuez les étapes décrites dans [Installer l’extension GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).
    
       ![Illustration des étapes à faire pour ajouter une extension GPU sous l’onglet Avancé dans Ajouter une machine virtuelle. Les options permettant de sélectionner et d’ajouter une extension sont mises en évidence.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-01.png)

       L’onglet **Avancé** affiche l’extension que vous avez sélectionnée.

       ![Capture d’écran de l’onglet Avancé dans Ajouter une machine virtuelle. L’extension GPU qui a été installée est mise en évidence.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-02.png)

1. Une fois la machine virtuelle GPU créée, elle apparaît dans la liste des machines virtuelles de votre ressource Azure Stack Edge dans le portail Azure.

    ![Capture d’écran de la vue Machines virtuelles dans Azure Stack Edge, mettant en évidence une machine virtuelle GPU nouvellement créée.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

    Sélectionnez la machine virtuelle et affichez ses détails. Vérifiez que l’extension GPU est dans l’état **Réussite**.

    ![Capture d’écran du volet Détails pour une machine virtuelle Azure Stack Edge. La section Extensions installées, avec une extension GPU installée, est mise en évidence.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/vm-details-extension-installed.png)


### <a name="templates"></a>[Modèles](#tab/templates)

Pour déployer des machines virtuelles GPU sur votre appareil à l’aide de modèles Azure Resource Manager, effectuez ces étapes :

1. [Téléchargez les modèles de machine virtuelle et les fichiers de paramètres](https://aka.ms/ase-vm-templates) sur votre ordinateur client. Décompressez-les dans le répertoire qui vous servira de répertoire de travail.

1. Avant de pouvoir déployer des machines virtuelles sur votre appareil Azure Stack Edge, vous devez configurer votre client pour qu’il se connecte à l’appareil via Azure Resource Manager sur Azure PowerShell. Pour obtenir des instructions détaillées, consultez [Se connecter à Azure Resource Manager sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-connect-resource-manager.md).

1. Pour créer des machines virtuelles GPU, effectuez toutes les étapes décrites dans [Déployer des machines virtuelles sur votre appareil Azure Stack Edge au moyen de modèles](azure-stack-edge-gpu-deploy-virtual-machine-templates.md), en respectant la configuration requise suivante : 
            
    - Quand vous spécifiez des tailles de machine virtuelle GPU, veillez à spécifier des tailles de la série NCasT4-v3 dans `CreateVM.parameters.json`, qui sont les tailles de machine virtuelle GPU prises en charge. Pour plus d’informations, consultez [Tailles de machine virtuelle prises en charge pour les machines virtuelles GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

       ```json
           "vmSize": {
         "value": "Standard_NC4as_T4_v3"
       },
       ```

    Une fois la machine virtuelle GPU créée, elle apparaît dans la liste des machines virtuelles de votre ressource Azure Stack Edge dans le portail Azure.

    ![Capture d’écran de la vue Machines virtuelles dans Azure Stack Edge. Une machine virtuelle GPU nouvellement créée est mise en évidence.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

1. Sélectionnez la machine virtuelle et affichez ses détails. Copiez l’adresse IP allouée à la machine virtuelle.

    ![Capture d’écran du volet Détails pour une machine virtuelle Azure Stack Edge. La colonne Adresse IP, sous Réseau, est mise en évidence.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-of-virtual-machine.png)

Une fois que vous avez créé la machine virtuelle, vous pouvez [déployer l’extension GPU à l’aide du modèle d’extension](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).

---

> [!NOTE]
> Lors de la mise à jour de la version logicielle de votre appareil depuis la version 2012 vers une version ultérieure, vous devez arrêter manuellement les machines virtuelles GPU.

## <a name="install-gpu-extension-after-deployment"></a>Installer l’extension GPU après le déploiement

Pour tirer parti des fonctionnalités GPU de machines virtuelles de la série N Azure, installez des pilotes GPU Nvidia. Par le biais du portail Azure, vous pouvez installer l’extension GPU pendant ou après le déploiement de la machine virtuelle. Si vous utilisez des modèles, vous devez installer l’extension GPU après avoir créé la machine virtuelle.

---

### <a name="portal"></a>[Portail](#tab/portal)

Si vous n’avez pas installé l’extension GPU au moment de la création de la machine virtuelle, effectuez ces étapes pour l’installer maintenant sur la machine virtuelle déployée :

1. Accédez à la machine virtuelle à laquelle vous souhaitez ajouter l’extension GPU.

    ![Capture d’écran de la vue Machines virtuelles pour un appareil Azure Stack Edge, mettant en évidence une machine virtuelle dans la liste des machines virtuelles.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-01.png)
  
1. Dans **Détails**, sélectionnez **+ Ajouter une extension**. Sélectionnez ensuite l’extension GPU à installer.

    Les extensions GPU sont uniquement disponibles pour les machines virtuelles ayant une [taille de machine virtuelle de la série NCasT4-v3](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview). Si vous préférez, vous pouvez [installer l’extension GPU après le déploiement](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment).

![Illustration montrant les deux étapes permettant d’ajouter une extension GPU à une machine virtuelle sur un appareil Azure Stack Edge en utilisant le bouton « + Ajouter une extension » dans le volet « Détails » de la machine virtuelle.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-02.png)

> [!Note]
> Vous ne pouvez pas supprimer une extension GPU via le portail. Pour cela, vous devez utiliser l’applet de commande [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension?view=azurermps-6.13.0&preserve-view=true) dans Azure PowerShell. Pour obtenir des instructions, consultez [Supprimer l’extension GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md#remove-gpu-extension)

### <a name="templates"></a>[Modèles](#tab/templates)

Si vous créez une machine virtuelle GPU à l’aide de modèles, installez l’extension GPU seulement après avoir déployé la machine virtuelle. Si vous souhaitez avoir une procédure détaillée de l’utilisation de modèles pour déployer une extension GPU sur une machine virtuelle Windows ou sur une machine virtuelle Linux, consultez [Installer l’extension GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).

---

## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de déploiement des machines virtuelles](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Résoudre les problèmes d’extension GPU](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)
- [Superviser l’activité des machines virtuelles sur votre appareil](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Superviser l’utilisation du processeur et de la mémoire sur une machine virtuelle](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)
