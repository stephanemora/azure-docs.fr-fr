---
title: Partage de GPU sur un appareil Azure Stack Edge Pro GPU
description: Décrit les approches du partage de GPU sur un appareil Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6683e39cfa3601b1ae1fbbe02e69e4dc0a54e8e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564600"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>Partage de GPU sur votre appareil Azure Stack Edge Pro GPU

Le processeur graphique (GPU) est un processeur spécialisé conçu pour accélérer l’affichage graphique. Les GPU peuvent traiter de nombreux éléments de données simultanément, ce qui les rend utiles pour les applications de Machine Learning, de montage vidéo et de jeu. En plus de l’utilisation de l’UC pour le calcul à usage général, vos appareils Azure Stack Edge Pro GPU peuvent contenir une ou deux GPU NVIDIA Tesla T4 pour des charges de travail nécessitant beaucoup de ressources systèmes, telles que l’inférence accélérée de matériel. Pour plus d’informations, consultez [GPU Tesla T4 de NVIDIA](https://www.nvidia.com/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>À propos du partage de GPU

De nombreuses charges de travail de Machine Learning ou de calcul peuvent ne pas avoir besoin de GPU dédié. Un GPU peut être partagé, et le partage de GPU entre plusieurs charges de travail de machine virtuelle ou conteneurisées permet d’augmenter l’utilisation du GPU sans que cela affecte de manière significative les avantages en matière de performances de celui-ci.  

## <a name="using-gpu-with-vms"></a>Utilisation du GPU avec des machines virtuelles

Sur votre appareil Azure Stack Edge Pro, un GPU ne peut pas être partagé lors du déploiement de charges de travail de machine virtuelle. Un GPU ne peut être mappé qu’à une seule machine virtuelle. Cela implique que vous ne pouvez avoir qu’une seule machine virtuelle GPU sur un appareil avec un GPU, et deux machines virtuelles sur un appareil équipé de deux GPU. D’autres facteurs doivent également être pris en considération lors de l’utilisation de machines virtuelles GPU sur un appareil disposant de Kubernetes configuré pour les charges de travail conteneurisées. Pour plus d’informations, consultez [Machines virtuelles GPU et Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>Utilisation de GPU avec des conteneurs

Si vous déployez des charges de travail conteneurisées, un GPU peut être partagé de plusieurs façons au niveau de la couche matérielle et logicielle. Avec le GPU Tesla T4 sur votre appareil Azure Stack Edge Pro, nous sommes limités au partage logiciel. Sur votre appareil, les deux approches suivantes du partage logiciel de GPU sont utilisées : 

- La première approche implique l’utilisation de variables d’environnement pour spécifier le nombre de GPU pouvant être partagés dans le temps. Lors de l’utilisation de cette approche, tenez compte des avertissements suivants :

    - Cette méthode vous permet de spécifier un GPU, les deux GPU ou aucun GPU. Il n’est pas possible de spécifier une utilisation fractionnaire.
    - Il est possible de mapper plusieurs modules à un GPU, mais un même module ne peut pas être mappé à plusieurs GPU.
    - Avec la sortie de la NVIDIA SMI, vous pouvez voir l’utilisation globale du GPU, dont l’utilisation de la mémoire.
    
    Pour plus d’informations, découvrez comment [Déployer un module IoT Edge utilisant un GPU](azure-stack-edge-gpu-configure-gpu-modules.md) sur votre appareil.

- La deuxième approche exige que vous activiez le service multiprocessus sur vos GPU NVIDIA. MPS est un service de runtime qui permet à plusieurs processus utilisant CUDA de s’exécuter simultanément sur un seul GPU partagé. MPS permet le chevauchement d’opérations de noyau et memcopy à partir de différents processus sur le GPU pour atteindre une utilisation maximale. Pour en savoir plus, consultez la rubrique [Service multiprocessus](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

    Lors de l’utilisation de cette approche, tenez compte des avertissements suivants :
    
    - MPS vous permet de spécifier plus indicateurs dans un déploiement de GPU.
    - Vous pouvez spécifier une utilisation fractionnaire via MPS, ce qui limite l’utilisation de chaque application déployée sur l’appareil. Vous pouvez spécifier le pourcentage de GPU à utiliser pour chaque application dans la section `env` du fichier `deployment.yaml` en ajoutant le paramètre suivant : 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>Utilisation du GPU
 
Lorsque vous partagez un GPU sur des charges de travail conteneurisées déployées sur votre appareil, vous pouvez utiliser l’interface de gestion de système Nvidia (nvidia-smi). nvidia-smi est un utilitaire de ligne de commande qui vous permet de gérer et surveiller les appareils GPU Nvidia. Pour plus d’informations, consultez [Interface de gestion de système Nvidia](https://developer.nvidia.com/nvidia-system-management-interface).

Pour afficher l’utilisation du GPU, commencez par vous connecter à l’interface PowerShell de l’appareil. Exécutez la commande `Get-HcsNvidiaSmi` et affichez la sortie de Nvidia SMI. Vous pouvez également voir comment l’utilisation du GPU change en activant MPS, puis en déployant plusieurs charges de travail sur l’appareil. Pour en savoir plus, consultez [Activer un service multiprocessus](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps).


## <a name="next-steps"></a>Étapes suivantes

- [Partage de GPU pour des déploiements Kubernetes sur votre Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- [Partage de GPU pour des déploiements IOT sur votre Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
