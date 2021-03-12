---
title: Tailles de machines virtuelles Azure pour les field programmable gate arrays (FPGA)
description: Répertorie les différentes tailles de FPGA optimisées qui sont disponibles pour les machines virtuelles dans Azure. Répertorie des informations sur le nombre de processeurs virtuels, de disques de données et de cartes réseau, ainsi que sur le débit de stockage et la bande passante réseau pour les tailles disponibles dans cette série.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557673"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>Tailles de machine virtuelle optimisées pour FPGA

Les tailles de machine virtuelle optimisées pour FPGA sont des machines virtuelles spécialisées disponibles avec des FPGA uniques ou multiples. Ces tailles sont conçues pour les charges de travail gourmandes en calcul. Cet article donne des informations sur le nombre et le type de FPGA, de processeurs virtuels, de disques de données et de cartes réseau. Le débit de stockage et la bande passante réseau sont également inclus pour chacune des tailles de ce regroupement.

- Les tailles de la [série NP](np-series.md) sont optimisées pour les charges de travail, y compris l’inférence Machine Learning, le transcodage vidéo et la recherche et l’analytique de base de données. La série NP fonctionne grâce à des accélérateurs Xilinx U250.


## <a name="deployment-considerations"></a>Points à prendre en considération pour le déploiement

- Pour connaître la disponibilité des machines virtuelles, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

- Les machines virtuelles de série N ne peuvent être déployées que dans le modèle de déploiement Resource Manager.

- Si vous voulez déployer plus de quelques machines virtuelles de série N, envisagez de souscrire un abonnement de paiement à l’utilisation ou d’autres options d’achat. Si vous utilisez un [compte gratuit Azure](https://azure.microsoft.com/free/), vous pouvez seulement utiliser un nombre limité de cœurs de calcul Azure.

- Vous devrez peut-être augmenter le quota de cœurs (par région) dans votre abonnement Azure et le quota séparé des cœurs NP. Pour demander une augmentation de quota, [ouvrez une demande de service clientèle en ligne](../azure-portal/supportability/how-to-create-azure-support-request.md) gratuitement. Les limites par défaut peuvent varier en fonction de la catégorie de votre abonnement.

## <a name="other-sizes"></a>Autres tailles

- [Usage général](sizes-general.md)
- [Optimisé pour le calcul](sizes-compute.md)
- [Calcul accéléré du GPU](sizes-gpu.md)
- [Calcul haute performance](sizes-hpc.md)
- [Mémoire optimisée](sizes-memory.md)
- [Optimisé pour le stockage](sizes-storage.md)
- [Générations précédentes](sizes-previous-gen.md)

## <a name="next-steps"></a>Étapes suivantes

Lisez-en davantage sur les [Unités de calcul Azure (ACU)](acu.md) pour découvrir comment comparer les performances de calcul entre les références Azure.
