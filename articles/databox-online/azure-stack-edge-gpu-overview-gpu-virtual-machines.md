---
title: Vue d’ensemble des machines virtuelles GPU sur votre appareil Azure Stack Edge Pro GPU
description: Décrit l’utilisation des machines virtuelles optimisées pour les charges de travail accélérées par GPU sur Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: alkohli
ms.openlocfilehash: d8660ace369fe0da0da384dceae6bff35594df74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532262"
---
# <a name="gpu-virtual-machines-for-azure-stack-edge-pro-gpu-devices"></a>Machines virtuelles GPU pour les appareils Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Les charges de travail accélérées par GPU sur un appareil Azure Stack Edge Pro GPU requièrent une machine virtuelle GPU. Cet article fournit une vue d’ensemble des machines virtuelles GPU, y compris les systèmes d’exploitation pris en charge, les pilotes GPU et les tailles de machine virtuelle. Les options de déploiement des machines virtuelles GPU utilisées avec les clusters Kubernetes sont également abordées.

## <a name="about-gpu-vms"></a>À propos des machines virtuelles GPU

Vos appareils Azure Stack Edge peuvent être équipés de 1 ou 2 GPU Tesla T4 de NVIDIA. Pour déployer des charges de travail de machine virtuelle avec accélération GPU sur ces appareils, utilisez des tailles de machine virtuelle optimisées pour le GPU. Par exemple, la série NC T4 v3 doit être utilisée pour déployer des charges de travail d’inférence comprenant des GPU T4. Pour plus d’informations, consultez [Machines virtuelles de la série NC T4 v3](../virtual-machines/nct4-v3-series.md).

Pour tirer parti des fonctionnalités GPU de machines virtuelles de la série N Azure, installez des pilotes GPU Nvidia. L’extension du pilote GPU Nvidia installe les pilotes CUDA ou GRID Nvidia appropriés. Vous pouvez [installer les extensions GPU à l’aide de modèles ou via le portail Azure](#gpu-vm-deployment).

Vous pouvez [installer et gérer l’extension à l’aide des modèles Azure Resource Manager](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md) après le déploiement des VM. Dans le Portail Azure, vous pouvez installer l’extension GPU pendant ou après le déploiement d’une machine virtuelle. pour obtenir des instructions, consultez [Déployer des machines virtuelles GPU sur votre appareil Azure Stack Edge](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).

Si un cluster Kubernetes est configuré sur votre appareil, veillez à passer en revue les [considérations relatives au déploiement pour les clusters Kubernetes avant de](#gpu-vms-and-kubernetes) déployer les machines virtuelles GPU.

## <a name="supported-os-and-gpu-drivers"></a>Pilotes GPU et systèmes d’exploitation pris en charge 

Les extensions du pilote GPU Nvidia pour Windows et Linux prennent en charge les versions de système d’exploitation suivantes.

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

## <a name="gpu-vm-deployment"></a>Déploiement de machine virtuelle GPU

Vous pouvez déployer une machine virtuelle GPU par le biais du portail Azure ou en utilisant des modèles Azure Resource Manager. L’extension GPU est installée après la création de la machine virtuelle.<!--Wording still needs work!-->

- **Portail :** Dans le portail Azure, vous pouvez [installer rapidement l’extension GPU lorsque vous créez une machine virtuelle](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms) ou [après un déploiement de machine virtuelle]().<!--Can they remove the GPU extension. Tomorrow, create a new GPU VM to test.-->

- **Modèles :** À l’aide de modèles Azure Resource Manager, [vous créez une machine virtuelle](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment), puis vous [installez l’extension GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).


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

## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [Déployer des VM GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).
- Découvrez comment [Installer l’extension GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md) sur les machines virtuelles GPU exécutées sur votre appareil.
