---
title: Vue d’ensemble et déploiement des machines virtuelles GPU sur votre appareil Azure Stack Edge Pro GPU
description: Explique comment créer et gérer des machines virtuelles GPU sur un appareil Azure Stack Edge Pro GPU à l’aide de modèles.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/28/2021
ms.author: alkohli
ms.openlocfilehash: 754cb296d6edebe4a8026df612fc52113e171a1c
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663873"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Déployer des machines virtuelles GPU sur votre appareil Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Cet article fournit une vue d’ensemble des machines virtuelles GPU sur votre appareil Azure Stack Edge Pro GPU. L’article explique également comment créer une machine virtuelle GPU à l’aide des modèles d’Azure Resource Manager. 


## <a name="about-gpu-vms"></a>À propos des machines virtuelles GPU

Vos appareils Azure Stack Edge peuvent être équipés de 1 ou 2 GPU Tesla T4 de NVIDIA. Pour déployer des charges de travail de machine virtuelle avec accélération GPU sur ces appareils, utilisez des tailles de machine virtuelle optimisées pour le GPU. Par exemple, la série NC T4 v3 doit être utilisée pour déployer des charges de travail d’inférence comprenant des GPU T4. 

Pour plus d’informations, consultez [Machines virtuelles de la série NC T4 v3](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Pilotes GPU et systèmes d’exploitation pris en charge 

Pour tirer parti des fonctionnalités GPU de machines virtuelles de la série N Azure, installez des pilotes GPU Nvidia. 

L’extension du pilote GPU Nvidia installe les pilotes CUDA ou GRID Nvidia appropriés. Vous pouvez installer ou gérer l’extension à l’aide des modèles Azure Resource Manager.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Systèmes d’exploitation pris en charge pour l’extension GPU pour Windows

Cette extension prend en charge les systèmes d’exploitation suivants. D’autres versions peuvent fonctionner, mais elles n’ont pas été testées en interne sur des machines virtuelles GPU exécutées sur des appareils Azure Stack Edge.

| Distribution | Version |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Systèmes d’exploitation pris en charge pour l’extension GPU pour Linux

Cette extension prend en charge les distributions suivantes de système d’exploitation, en fonction de la prise en charge par le pilote de la version spécifique du système d’exploitation. D’autres versions peuvent fonctionner, mais elles n’ont pas été testées en interne sur des machines virtuelles GPU exécutées sur des appareils Azure Stack Edge.


| Distribution | Version |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>Machines virtuelles GPU et Kubernetes

Avant de déployer des machines virtuelles GPU sur votre appareil, passez en revue les considérations suivantes si Kubernetes est configuré sur l’appareil.

#### <a name="for-1-gpu-device"></a>Pour un appareil avec un GPU : 

- **Procéder à la création d’une machine virtuelle GPU, puis à la configuration de Kubernetes sur votre appareil** : dans ce scénario, la création de la machine virtuelle GPU et la configuration de Kubernetes réussissent. Dans ce cas, Kubernetes n’a pas accès au GPU.

- **Procéder à la configuration de Kubernetes sur votre appareil, puis à la création d’une machine virtuelle GPU** : dans ce scénario, Kubernetes revendique le GPU sur votre appareil et la création de la machine virtuelle échoue, car aucune ressource GPU n’est disponible.

#### <a name="for-2-gpu-device"></a>Pour un appareil avec deux GPU :

- **Procéder à la création d’une machine virtuelle GPU, puis à la configuration de Kubernetes sur votre appareil** : dans ce scénario, la machine virtuelle GPU que vous créez revendique un GPU sur votre appareil et la configuration Kubernetes réussit également et revendique un GPU. 

- **Procéder à la création de deux machines virtuelles GPU, puis à la configuration de Kubernetes sur votre appareil** : Dans ce scénario, les deux machines virtuelles GPU revendiquent les deux GPU sur l’appareil et la configuration de Kubernetes réussit sans GPU. 

- **Procéder à la configuration de Kubernetes sur votre appareil, puis à la création d’une machine virtuelle GPU** : dans ce scénario, Kubernetes revendique les deux GPU sur votre appareil et la création de la machine virtuelle échoue, car aucune ressource GPU n’est disponible.

<!--Li indicated that this is fixed. If you have GPU VMs running on your device and Kubernetes is also configured, then anytime the VM is deallocated (when you stop or remove a VM using Stop-AzureRmVM or Remove-AzureRmVM), there is a risk that the Kubernetes cluster will claim all the GPUs available on the device. In such an instance, you will not be able to restart the GPU VMs deployed on your device or create GPU VMs. -->


## <a name="create-gpu-vms"></a>Créer des machines virtuelles GPU

Pour déployer des machines virtuelles GPU sur votre appareil, procédez comme suit :

1. Identifiez si votre appareil doit également exécuter Kubernetes. Si l’appareil doit exécuter Kubernetes, vous devez d’abord créer la machine virtuelle GPU, puis configurer Kubernetes. Si Kubernetes est configuré en premier, il revendique toutes les ressources GPU disponibles et la création de la machine virtuelle GPU échoue.

1. [Téléchargez les modèles de machine virtuelle et les fichiers de paramètres](https://aka.ms/ase-vm-templates) sur votre ordinateur client. Décompressez-les dans le répertoire qui vous servira de répertoire de travail.

1. Avant de pouvoir déployer des machines virtuelles sur votre appareil Azure Stack Edge, vous devez configurer votre client pour qu’il se connecte à l’appareil via Azure Resource Manager sur Azure PowerShell. Pour obtenir des instructions détaillées, consultez [Se connecter à Azure Resource Manager sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-connect-resource-manager.md).

1. Pour créer des machines virtuelles GPU, suivez toutes les étapes de la section [Déployer une machine virtuelle sur votre Azure Stack Edge à l’aide de modèles](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) ou [Déployer une machine virtuelle sur votre Azure Stack Edge à l’aide du Portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md), à l’exception des différences suivantes : 

            
    1. Si vous créez une machine virtuelle à l’aide des modèles, quand vous spécifiez des tailles de machine virtuelle GPU, veillez à utiliser la série NCasT4-v3 dans `CreateVM.parameters.json`, car elles sont prises en charge pour les machines virtuelles GPU. Pour plus d’informations, consultez [Tailles de machine virtuelle prises en charge pour les machines virtuelles GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```
        Si vous utilisez la Portail Azure pour créer votre machine virtuelle, vous pouvez toujours sélectionner une taille de machine virtuelle à partir de la série NCasT4-v3.

    1. Une fois la machine virtuelle GPU créée, elle apparaît dans la liste des machines virtuelles de votre ressource Azure Stack Edge dans le portail Azure.

        ![Machine virtuelle GPU dans la liste des machines virtuelles dans le portail Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Sélectionnez la machine virtuelle pour afficher ses détails. Copiez l’adresse IP allouée à la machine virtuelle.

    ![Adresse IP allouée à la machine virtuelle GPU dans le portail Azure](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Si nécessaire, vous pouvez rétablir le réseau de calcul sur la configuration requise. 


Une fois la machine virtuelle créée, vous pouvez déployer l’extension GPU à l’aide du modèle d’extension.


> [!NOTE]
> Lors de la mise à jour de la version logicielle de votre appareil depuis la version 2012 vers une version ultérieure, vous devez arrêter manuellement les machines virtuelles GPU.



## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [Installer l’extension GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md) sur les machines virtuelles GPU exécutées sur votre appareil.
