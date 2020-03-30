---
title: Tailles des machines virtuelles – GPU | Microsoft Docs
description: Répertorie les différentes tailles de GPU optimisées disponibles pour les machines virtuelles dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
services: virtual-machines
documentationcenter: ''
author: vikancha
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 7e1e0d488844a94bd0be2b91398678e620295729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913580"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Tailles de machine virtuelle à GPU optimisé

Les tailles de machine virtuelle au GPU optimisé sont des machines virtuelles spécialisées disponibles avec des GPU NVIDIA uniques ou multiples. Ces tailles sont conçues pour des charges de travail de visualisation, mais également de calcul et d’affichage graphique intensifs. Cet article donne des informations sur le nombre et le type de GPU, de processeurs virtuels, de disques de données et de cartes réseau. Le débit de stockage et la bande passante réseau sont également inclus pour chacune des tailles de ce regroupement.

- Les tailles des séries [NC](nc-series.md), [NCv2](ncv2-series.md) et [NCv3](ncv3-series.md) sont optimisées pour les algorithmes et les applications nécessitant beaucoup de ressources réseau et de calculs. En voici quelques exemples : les applications et les simulations CUDA et OpenCL, l’intelligence artificielle et l’apprentissage profond (Deep Learning). Équipée du GPU Tesla V100 de NVIDIA, la série NCv3 est axée sur les charges de travail informatiques à hautes performances. La série NC utilise le processeur Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) et les machines virtuelles de la série NCv2 et NCv3 sont dotées du processeur Intel Xeon E5-2690 v4 (Broadwell).

- Les tailles des séries [ND](nd-series.md) et [NDv2](ndv2-series.md) sont destinées à l’exécution de scénarios d’apprentissage et d’inférence pour l’apprentissage profond (Deep Learning). Elles utilisent le GPU NVIDIA Tesla P40 et le processeur Intel Xeon E5-2690 v4 (Broadwell). La série NDv2 utilise le processeur Intel Xeon Platinum 8168 (Skylake).

- Les tailles des séries [NV](nv-series.md) et [NVv3](nvv3-series.md) sont optimisées et conçues pour la visualisation à distance, la diffusion en continu, les jeux, l’encodage et les scénarios de VDI utilisant des infrastructures comme OpenGL ou DirectX. Ces machines virtuelles reposent sur le GPU Tesla M60 de NVIDIA.

- Les tailles de machines virtuelles de la série [NVv4](nvv4-series.md) sont optimisées et conçues pour l’infrastructure VDI et la visualisation à distance. Avec des GPU partitionnés, la série NVv4 offre la taille adaptée aux charges de travail nécessitant des ressources GPU plus petites. Ces machines virtuelles sont associées au GPU AMD Radeon Instinct MI25. Les machines virtuelles NVv4 prennent actuellement en charge uniquement le système d’exploitation invité Windows.

## <a name="supported-operating-systems-and-drivers"></a>Systèmes d’exploitation et pilotes pris en charge

Pour tirer parti des fonctionnalités GPU de machines virtuelles de la série N Azure, installez des pilotes GPU NVIDIA.

[L’extension du pilote GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) installe les pilotes CUDA ou GRID NVIDIA appropriés sur une machine virtuelle de série N. Installez ou gérez l’extension à l’aide du portail Azure ou d’outils tels qu’Azure PowerShell ou les modèles Azure Resource Manager. Consultez la [documentation sur l’extension du pilote GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) pour connaître les systèmes d’exploitation pris en charge et les étapes de déploiement. Pour des informations générales sur les extensions de machine virtuelle, consultez [Extensions et fonctionnalités des machines virtuelles Azure](/azure/virtual-machines/extensions/overview).

Si vous choisissez d’installer manuellement les pilotes GPU NVIDIA, voir [Configuration des pilotes GPU de série N pour Windows](/azure/virtual-machines/windows/n-series-driver-setup) ou [Configuration des pilotes GPU de série N pour Linux](/azure/virtual-machines/linux/n-series-driver-setup) pour connaître les systèmes d’exploitation et pilotes pris en charge, ainsi que les étapes d’installation et de vérification.

## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

- Pour connaître la disponibilité des machines virtuelles, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

- Les machines virtuelles de série N ne peuvent être déployées que dans le modèle de déploiement Resource Manager.

- Les machines virtuelles de série N diffèrent en fonction du type de stockage Azure qu’elles prennent en charge pour les disques. Les machines virtuelles NC et NV ne prennent en charge que les disques de machines virtuelles sauvegardés par le Stockage sur disque Standard (HDD). Les machines virtuelles NCv2, NCv3, ND, NDv2 et NVv2 prennent en charge uniquement les disques de machine virtuelle avec un stockage sur disque (SSD) Premium.

- Si vous voulez déployer plus de quelques machines virtuelles de série N, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

- Vous devrez probablement augmenter le quota de cœurs (par région) de votre abonnement Azure, ainsi que le quota pour les cœurs NC, NCv2, NCv3, ND, NDv2, NV ou NVv2. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitement. Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Calcul haute performance](sizes-hpc.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
