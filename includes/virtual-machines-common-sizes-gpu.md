---
title: Fichier Include
description: Fichier Include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 11/14/2018
ms.author: cynthn;azcspmt;jonbeck
ms.custom: include file
ms.openlocfilehash: fd37c0c118812bb3554b19fec9bf5ae3f33a2fdc
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54314097"
---
Les tailles de machine virtuelle au GPU optimisé sont des machines virtuelles spécialisées disponibles avec des GPU NVIDIA uniques ou multiples. Ces tailles sont conçues pour des charges de travail de visualisation, mais également de calcul et d’affichage graphique intensifs. Cet article donne des informations sur le nombre et le type de GPU, de processeurs virtuels, de disques de données et de cartes réseau. Le débit de stockage et la bande passante réseau sont également inclus pour chacune des tailles de ce regroupement. 

* Les tailles **NC, NCv2, NCv3, ND et NDv2** sont optimisées pour les algorithmes et les applications nécessitant beaucoup de ressources réseau et de calculs. En voici quelques exemples : les applications et les simulations CUDA et OpenCL, l’intelligence artificielle et l’apprentissage profond. Équipée du GPU Tesla V100 de NVIDIA, la série NCv3 est axée sur les charges de travail informatiques à hautes performances.  La série ND est destinée à l’exécution de scénarios d’apprentissage et d’inférence pour l’apprentissage profond. Elle utilise le GPU Tesla P40 de NVIDIA.

* La **série NC** comprend un processeur Intel Xeon® E5-2690 v3 2,6 GHz.

* Les tailles **NCSv3**, **NCSv2** et **ND** comprennent un processeur Intel Xeon® E5-2690 v4 2,6 GHz.
                      
* Les tailles **NV et NVv2** sont optimisées et conçues pour la visualisation à distance, la diffusion en continu, les jeux, l’encodage et les scénarios de VDI utilisant des infrastructures comme OpenGL ou DirectX.  Ces machines virtuelles reposent sur le GPU Tesla M60 de NVIDIA.


## <a name="nc-series"></a>Série NC

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les machines virtuelles de série NC sont optimisées par la carte [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf). Les utilisateurs peuvent exploiter plus rapidement leurs données en tirant parti de CUDA pour les applications d’exploration énergétique, les simulations de crash, le rendu de lancer de rayon, l’apprentissage profond et plus encore. La configuration NC24r fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.


| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- | ---- |
| Standard_NC6 |6. |56 | 340 | 1 | 8 | 24 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 | 16 | 48 | 2 |
| Standard_NC24 |24 |224 | 1 440 | 4 | 32 | 64 | 4 |
| Standard_NC24r* |24 |224 | 1 440 | 4 | 32 | 64 | 4 |

1 GPU = une moitié de carte K80.

*Prenant en charge RDMA

## <a name="ncv2-series"></a>Série NCv2

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

Les machines virtuelles de série NCv2 sont optimisées par les GPU [NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf). Ces GPU peuvent fournir des performances de calcul deux fois supérieures à celles de la série NC. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. La configuration NC24rs v2 fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) dans votre abonnement est défini au départ sur 0 dans chaque région. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | ---  | --- |
| Standard_NC6s_v2 |6. |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v2 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v2 |24 |448 | 2948 | 4 | 64 | 32 | 8 |
| Standard_NC24rs_v2* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = une carte P100.

*Prenant en charge RDMA

## <a name="ncv3-series"></a>Série NCv3

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

Les machines virtuelles de série NCv3 sont optimisées par les GPU [NVIDIA Tesla V100](http://www.nvidia.com/content/PDF/Volta-Datasheet.pdf). Ces GPU peuvent fournir des performances de calcul une fois et demie supérieures à celles de la série NCv2. Les clients peuvent tirer parti de ces GPU mis à jour pour les charges de travail HPC traditionnelles telles que la modélisation de gisements, le séquençage de l’ADN, l’analyse des protéines, les simulations de Monte-Carlo, etc. La configuration NC24rs v3 fournit une interface réseau à haut débit et à faible latence optimisée pour les charges de travail d’informatique parallèle fortement couplées.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) dans votre abonnement est défini au départ sur 0 dans chaque région. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v3 |6. |112 | 736 | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3 |12 |224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3 |24 |448 | 2948 | 4 | 64 | 32 | 8 | 
| Standard_NC24rs_v3* |24 |448 | 2948 | 4 | 64 | 32 | 8 |

1 GPU = une carte V100.

*Prenant en charge RDMA

## <a name="ndv2-series-preview"></a>NDv2-series (préversion)


Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

InfiniBand : Non pris en charge


Les machines virtuelles de la série NDv2 rejoignent la famille de processeurs graphiques (GPU) pour répondre aux besoins des charges de travail d’intelligence artificielle, HPC et Machine Learning. Elles sont dotées de 8 GPU interconnectés NVIDIA Tesla V100 NVLINK, de 40 cœurs Intel Skylake et de 672 Gio de mémoire système. L’instance NDv2 offre d’excellentes performances FP32 et FP64 pour les charges de travail HPC et d’intelligence artificielle utilisant les frameworks Cuda, TensorFlow, Pytorch, Caffe et autres.

[Inscrivez-vous et accédez à ces machines lors de la préversion](https://aka.ms/ndv2signup).
<br>


| Taille              | Processeurs virtuels | GPU              | Mémoire  | Cartes réseau (max) | Bande passante taille du disque           | Bande passante disques de données (1 023 Go chacun) | Bande passante réseau maximale | 
|-------------------|------|------------------|---------|------------|--------------------------|--------------------------------|-----------------------|
| Standard_ND40s_v2 | 40   | 8 V100 (NVlilnk) | 672 Gio | 8          | 1344 / 2948XIO temporaires | 32                             | 24 000 Mbits/s           |

## <a name="nd-series"></a>Série ND

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

Les machines virtuelles de la série ND sont nouvelles dans la famille de GPU et sont conçues pour les charges de travail d’intelligence artificielle et d’apprentissage profond. Elles offrent d’excellentes performances pour l’apprentissage et l’inférence. Les instances ND sont optimisées par des GPU [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf). Ces instances offrent d’excellentes performances pour les opérations à virgule flottante simple précision, et pour les charges de travail d’intelligence artificielle utilisant Microsoft Cognitive Toolkit, TensorFlow, Caffe et d’autres infrastructures. La série ND offre également une taille de mémoire GPU beaucoup plus importante (24 Go), ce qui permet d’adapter des modèles de réseaux neuronaux beaucoup plus volumineux. À l’instar de la série NC, la série ND offre une configuration avec un réseau à faible latence secondaire et à haut débit grâce à l’accès direct à la mémoire à distance (RDMA), ainsi que la connectivité InfiniBand, de sorte que vous pouvez exécuter des travaux de formation à grande échelle s’étendant sur de nombreux GPU.

> [!IMPORTANT]
> Pour cette famille de tailles, le quota de processeurs virtuels (cœurs) par région dans votre abonnement est défini au départ sur 0. [Demandez une augmentation du quota de processeurs virtuels](../articles/azure-supportability/resource-manager-core-quotas-request.md) pour cette famille dans une [région disponible](https://azure.microsoft.com/regions/services/).
>

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6. |112 | 736 | 1 | 24 | 12 | 4 |
| Standard_ND12s |12 |224 | 1474 | 2 | 48 | 24 | 8 | 
| Standard_ND24s |24 |448 | 2948 | 4 | 96 | 32 | 8 |
| Standard_ND24rs* |24 |448 | 2948 | 4 | 96 | 32 | 8 |

1 GPU = une carte P40.

*Prenant en charge RDMA

## <a name="nv-series"></a>Série NV

Premium Storage :  Non pris en charge

Mise en cache du Stockage Premium :  Non pris en charge

Les machines virtuelles de la série NV sont optimisées par des GPU [NVIDIA Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID pour les applications de bureautique accélérées et les bureaux virtuels où les clients peuvent visualiser leurs données ou simulations. Les utilisateurs peuvent visualiser leurs flux de travail nécessitant beaucoup de graphismes sur les instances NV afin d’obtenir des fonctionnalités graphiques de qualité supérieure et exécuter par ailleurs des charges de travail simple précision comme le codage et le rendu. 

Chaque GPU dans les instances NV est fourni avec une licence GRID. Cette licence vous donne la possibilité d’utiliser une instance NV comme station de travail virtuelle pour un seul utilisateur. 25 utilisateurs simultanés peuvent aussi se connecter à la machine virtuelle pour un scénario d’application virtuelle.

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | Stations de travail virtuelles | Applications virtuelles | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | 
| Standard_NV6 |6. |56 |340 | 1 | 8 | 24 | 1 | 1 | 25 |
| Standard_NV12 |12 |112 |680 | 2 | 16 | 48 | 2 | 2 | 50 |
| Standard_NV24 |24 |224 |1 440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = une moitié de carte M60.

## <a name="nvv2-series-preview"></a>Série NVv2 (préversion)

Premium Storage :  Pris en charge

Mise en cache du Stockage Premium :  Pris en charge

Les machines virtuelles de la série NVv2 sont pilotées par des GPU [Tesla M60 de NVIDIA](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) et la technologie NVIDIA GRID avec des processeurs Intel Broadwell. Ces machines virtuelles sont destinées aux bureaux virtuels et aux applications graphiques avec accélération par GPU sur lesquels les clients souhaitent visualiser leurs données, simuler des résultats à afficher, travailler en CAO ou effectuer la restitution ou la diffusion du contenu. Par ailleurs, ces machines virtuelles peuvent exécuter des charges de travail de précision unique, telles que le codage et le rendu. Les machines virtuelles NVv2 prennent en charge le Stockage Premium et offrent deux fois plus de mémoire RAM que la série NV précédente.  

Chaque GPU dans les instances NVv2 est fourni avec une licence GRID. Cette licence vous donne la possibilité d’utiliser une instance NV comme station de travail virtuelle pour un seul utilisateur. 25 utilisateurs simultanés peuvent aussi se connecter à la machine virtuelle pour un scénario d’application virtuelle.

| Taille | Processeurs virtuels | Mémoire : Gio | Stockage temporaire (SSD) en Gio | GPU | Mémoire GPU : Gio | Disques de données max. | Nombre max de cartes réseau | Stations de travail virtuelles | Applications virtuelles | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV6s_v2 |6. |112 |320 | 1 | 8 | 12 | 4 | 1 | 25 |
| Standard_NV12s_v2 |12 |224 |640 | 2 | 16 | 24 | 8 | 2 | 50 |
| Standard_NV24s_v2 |24 |448 |1 280 | 4 | 32 | 32 | 8 | 4 | 100 |

1 GPU = une moitié de carte M60.

 
