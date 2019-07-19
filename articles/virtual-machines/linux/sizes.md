---
title: Tailles des machines virtuelles Linux dans Azure | Microsoft Docs
description: Répertorie les différentes tailles disponibles pour les machines virtuelles Linux dans Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: da681171-f045-4c80-a5a9-d8bd47964673
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: jonbeck
ms.openlocfilehash: 7445b0ce2cc80a899ef5d34c01f37564d255b5ce
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708748"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Tailles des machines virtuelles Linux dans Azure
Cet article décrit les tailles et options disponibles pour les machines virtuelles Azure que vous pouvez utiliser pour exécuter vos applications et charges de travail Linux. Il expose également les points à prendre en considération pour le déploiement quand vous planifiez l’utilisation de ces ressources. Cet article est également disponible pour les [machines virtuelles Windows](../windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


| Type                     | Tailles           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Usage général](sizes-general.md)          | B, Dsv3, Dv3, DSv2, Dv2, Av2, DC  | Ratio processeur/mémoire équilibré. Idéal pour le test et le développement, les bases de données petites à moyennes et les serveurs web au trafic faible à moyen. |
| [Optimisé pour le calcul](sizes-compute.md)        | Fsv2           | Ratio processeur/mémoire élevé. Convient pour les serveurs web au trafic moyen, les appareils réseau, les processus de traitement par lots et les serveurs d’application.        |
| [Mémoire optimisée](sizes-memory.md)         | Esv3, Ev3, Mv2, M, DSv2, Dv2  | Ratio mémoire/processeur élevé. Idéal pour les serveurs de base de données relationnelle, les caches moyens à grands et l’analytique en mémoire.                 |
| [Optimisé pour le stockage](sizes-storage.md)        | Lsv2                | Débit et nombre d’E/S de disque élevés, idéal pour les Big Data, SQL, les bases de données NoSQL, l’entreposage de données et les grandes bases de données transactionnelles.  |
| [GPU](sizes-gpu.md)            | NC, NCv2, NCv3, ND, NDv2 (préversion), NV, NVv3 (préversion) | Machines virtuelles spécialisées, ciblées pour l’affichage de graphiques complexes et le montage vidéo, ainsi que pour la formation et l’inférence de modèles avec apprentissage approfondi. Disponible avec un ou plusieurs GPU.       |
| [Calcul haute performance](sizes-hpc.md) | HB, HC, H | Nos machines virtuelles les plus rapides et dotées des processeurs les plus puissants avec interfaces réseau haut débit en option (RDMA). |

<br>

- Pour plus d'informations sur la tarification des différentes tailles, consultez [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Pour la disponibilité des tailles de machine virtuelle dans les régions Azure, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).
- Pour connaître les limites générales des machines virtuelles Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../../azure-subscription-service-limits.md).
- Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.


## <a name="rest-api"></a>API REST

Pour plus d’informations sur l’utilisation de l’API REST pour demander la taille des machines virtuelles, consultez les rubriques suivantes :

- [Répertorier les tailles disponibles des machines virtuelles pour le redimensionnement](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes)
- [Répertorier les tailles disponibles des machines virtuelles pour un abonnement](https://docs.microsoft.com/rest/api/compute/resourceskus/list)
- [Répertorier les tailles de machine virtuelle disponibles dans un groupe à haute disponibilité](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.

## <a name="benchmark-scores"></a>Scores de test d’évaluation

En savoir plus sur les performances de calcul pour les machines virtuelles Linux à l’aide des [scores de test d’évaluation CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les différentes tailles de machines virtuelles qui sont disponibles :
- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- Consultez la page [Génération précédente](sizes-previous-gen.md) des séries A Standard, Dv1 (D1-4 et D11-14 v1) et A8-A11



