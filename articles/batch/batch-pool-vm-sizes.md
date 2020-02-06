---
title: Choisir des tailles de machines virtuelles pour des pools - Azure Batch | Microsoft Docs
description: Quelle taille de machine virtuelle choisir parmi celles disponibles pour les nœuds de calcul dans des pools Azure Batch
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.workload: ''
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: be19de19dab92bc40ca5529ad578e033a98929cd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023563"
---
# <a name="choose-a-vm-size-for-compute-nodes-in-an-azure-batch-pool"></a>Choisir une taille de machine virtuelle pour des nœuds de calcul dans un pool Azure Batch

Lorsque vous sélectionnez une taille de nœud pour un pool Azure Batch, vous avez le choix entre quasiment toutes les tailles de machine virtuelle disponibles dans Azure. Azure propose une gamme de tailles pour les machines virtuelles Windows et Linux pour différentes charges de travail.

Quelques exceptions et limites s’appliquent quant au choix d’une taille de machine virtuelle :

* Certaines séries ou tailles de machine virtuelle ne sont pas prises en charge dans Batch.
* Certaines tailles de machine virtuelle sont limitées et doivent être activées explicitement avant de pouvoir être allouées.

## <a name="supported-vm-series-and-sizes"></a>Tailles et séries de machine virtuelle prises en charge

### <a name="pools-in-virtual-machine-configuration"></a>Pools dans la configuration de la machine virtuelle

Les pools Batch dans la configuration de la machine virtuelle prennent en charge presque toutes les tailles de machine virtuelle ([Linux](../virtual-machines/linux/sizes.md), [Windows](../virtual-machines/windows/sizes.md)). Consultez le tableau suivant pour en savoir plus sur les tailles prises en charge et les restrictions.

La prise en charge de toute taille de machine virtuelle en préversion ou en promotion non répertoriée n’est pas garantie.

| Série de la machine virtuelle  | Tailles prises en charge | Mode d’allocation de pool de compte batch<sup>1</sup> |
|------------|---------|-----------------|
| Série A de base | Toutes les tailles *sauf* Basic_A0 (A0) | Quelconque |
| Série A | Toutes les tailles *sauf* Standard_A0 | Quelconque |
| Série Av2 | Toutes les tailles | Quelconque |
| Série B | None | Non disponible |
| Série DC | None | Non disponible |
| Série Dv2, DSv2 | Toutes les tailles | Quelconque |
| Série Dv3, Dsv3 | Toutes les tailles | Quelconque |
| Série Ev3, Esv3 | Toutes les tailles | Quelconque |
| Série Fsv2 | Toutes les tailles | Quelconque |
| Série H | Toutes les tailles | Quelconque |
| Série HB<sup>2</sup> | Toutes les tailles | Quelconque |
| Série HC<sup>2</sup> | Toutes les tailles | Quelconque |
| Série Ls | Toutes les tailles | Quelconque |
| Série Lsv2 | None | Non disponible |
| Série M | Standard_M64ms (priorité basse uniquement), Standard_M128s (priorité basse uniquement) | Quelconque |
| Série Mv2 | None | Non disponible |
| Série NC | Toutes les tailles | Quelconque |
| Série NCv2<sup>2</sup> | Toutes les tailles | Quelconque |
| Série NCv3<sup>2</sup> | Toutes les tailles | Quelconque |
| Série ND<sup>2</sup> | Toutes les tailles | Quelconque |
| Série NDv2 | Toutes les tailles | Mode Abonnement utilisateur |
| Série NV | Toutes les tailles | Quelconque |
| Série NVv3 | None | Non disponible |
| SAP HANA | None | Non disponible |

<sup>1</sup> Certaines séries de machines virtuelles plus récentes sont au départ partiellement prises en charge. Ces séries de machines virtuelles peuvent être allouées par comptes Batch avec le **mode d’allocation de pool** défini sur **abonnement utilisateur**. Consultez [Gérer des comptes Batch](batch-account-create-portal.md#additional-configuration-for-user-subscription-mode) pour en savoir plus sur la configuration du compte Batch. Consultez [Quotas et limites](batch-quota-limit.md) pour savoir comment demander des quotas pour ces séries de machines virtuelles partiellement prises en charge pour les comptes Batch d’**abonnement utilisateur**.  

<sup>2</sup> Ces tailles de machines virtuelles peuvent être allouées dans des pools Batch dans la configuration de la machine virtuelle, mais vous devez demander une augmentation du [quota spécifique](batch-quota-limit.md#increase-a-quota).

### <a name="pools-in-cloud-service-configuration"></a>Pools dans la configuration de service cloud

Les pools Batch dans la configuration de service cloud prennent en charge toutes les [tailles de machine virtuelle pour Services cloud](../cloud-services/cloud-services-sizes-specs.md), **sauf** les suivantes :

| Série de la machine virtuelle  | Tailles non prises en charge |
|------------|-------------------|
| Série A   | Très petite       |
| Série Av2 | Standard_A1_v2, Standard_A2_v2, Standard_A2m_v2 |

## <a name="size-considerations"></a>Considérations en matière de taille

* **Exigences pour l’application** : tenez compte des caractéristiques et des exigences de l’application que vous allez exécuter sur les nœuds. Des aspects tels que la nature multithread de l’application et le volume de mémoire utilisé vous aideront à déterminer la taille de nœud la mieux adaptée et la plus rentable. Pour les applications CUDA ou [charges de travail MPI](batch-mpi.md) avec plusieurs instances, pensez aux tailles de machine virtuelles [compatibles GPU](../virtual-machines/linux/sizes-hpc.md) ou [HPC](../virtual-machines/linux/sizes-gpu.md), respectivement. (Consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](batch-pool-compute-intensive-sizes.md).)

* **Tâches par nœud** : la taille du nœud est souvent sélectionnée en supposant qu’une tâche s’exécute sur un nœud à la fois. Cependant, plusieurs tâches (et par conséquent, plusieurs instances d’application) peuvent [s’exécuter en parallèle](batch-parallel-node-tasks.md) sur les nœuds de calcul lors de l’exécution du travail. Dans ce cas, il est courant de choisir une taille de nœud multicœur pour prendre en charge la demande accrue de l’exécution parallèle des tâches.

* **Charger des niveaux pour différentes tâches** : tous les nœuds dans un pool ont la même taille. Si vous prévoyez d’exécuter des applications dont la configuration système requise et/ou les niveaux de charge diffèrent, nous vous recommandons d’utiliser des pools distincts.

* **Disponibilité de la région** : une série ou une taille de machines virtuelles peut ne pas être disponible dans les régions où vous créez vos comptes Batch. Pour vérifier qu’une taille est disponible, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

* **Quotas** : les [quotas de cœurs](batch-quota-limit.md#resource-quotas) dans votre compte Batch peuvent limiter le nombre de nœuds d’une taille donnée que vous pouvez ajouter à un pool Batch. Pour demander une augmentation du quota, consultez [cet article](batch-quota-limit.md#increase-a-quota). 

* **Configuration du pool** : en général, vous avez plusieurs options de taille de machine virtuelle lorsque vous créez un pool dans la configuration de machine virtuelle, en comparaison avec la configuration de service cloud.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir une présentation détaillée de Batch, consultez [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).
* Pour plus d’informations sur l’utilisation de tailles de machine virtuelle nécessitant beaucoup de ressources système, consultez [Utiliser des instances compatibles RDMA ou GPU dans les pools Batch](batch-pool-compute-intensive-sizes.md).
