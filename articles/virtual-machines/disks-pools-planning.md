---
title: Optimiser les performances de vos pools de disques Azure (préversion)
description: Découvrez comment optimiser les performances d’un pool de disques Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 74d50826811198811e6cea671641cae378d1235c
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122535159"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Guide de planification des pools de disques Azure (préversion)

Avant de déployer un pool de disques Azure (préversion), il est important de comprendre les exigences de performances de votre charge de travail. La détermination de vos exigences à l’avance vous permet d’optimiser les performances de votre pool de disques. Les performances d’un pool de disques sont déterminées par trois facteurs principaux. L’ajustement de l’un d’eux a pour effet de modifier les performances de votre pool de disques. C’est facteurs sont les suivants :

- Cible de scalabilité du pool de disques
- Objectifs de scalabilité des disques individuels contenus dans le pool de disques
- Connexion réseau entre les machines clientes et le pool de disques.

## <a name="optimize-for-low-latency"></a>Optimiser pour une faible latence

Si vous privilégiez une faible latence, ajoutez des disques Ultra à votre pool de disques. Les disques Ultra offrent une latence inférieure à la milliseconde. Pour bénéficier de la latence la plus faible possible, vous devez également évaluer la configuration de votre réseau et vous assurer qu’elle utilise le chemin optimal. Pour minimiser la latence réseau, songez à utiliser [ExpressRoute FastPath](../expressroute/about-fastpath.md).

## <a name="optimize-for-high-throughput"></a>Optimiser pour un débit élevé

Si vous privilégiez le débit, commencez par évaluer le nombre de pools de disques requis pour atteindre vos cibles de débit. Une fois que vous disposez des cibles nécessaires, vous pouvez les fractionner entre les disques et leurs types. Actuellement, vous pouvez utiliser deux types de disques dans un pool de disques, à savoir SSD Premium et Ultra. Les disques SSD Premium offrent des niveaux d’IOPS et de Mbits/s élevés à l’échelle de leur capacité de stockage, tandis que les disques ULTRA peuvent mettre à l’échelle leurs performances indépendamment de leur capacité de stockage. Sélectionnez le type correspondant à l’équilibre coûts-performances que vous souhaitez. Vérifiez également que la connectivité réseau entre vos clients et le pool de disques ne constitue pas un goulot d’étranglement, en particulier en matière de débit.


## <a name="use-cases"></a>Cas d'utilisation

Le tableau suivant présente des cas d’usage classiques pour des pools de disques avec Azure VMware Solution et une configuration recommandée.


|Cas d’usage d’Azure VMware Solution  |Type de disque suggéré  |Configuration réseau suggérée  |
|---------|---------|---------|
|Stockage par blocs pour les pages de travail actives, comme une extension de vSAN Azure VMware Solution.     |Disques Ultra         |Utilisez une passerelle de réseau virtuel ExpressRoute Ultra-performance ou ErGw3AZ (10 Gbits/s) pour connecter le réseau virtuel du pool de disques au cloud Azure VMware Solution et permettre à FastPath de réduire la latence du réseau.         |
|Hiérarchisation : hiérarchiser les données rarement utilisées sur le pool de disques à partir du vSAN Azure VMware Solution.     |SSD Premium         |Utiliser la passerelle de réseau virtuel ExpressRoute Standard (1 Gbits/s) ou Haute performance (2 Gbits/s) pour connecter le réseau virtuel du pool de disques au cloud Azure VMware Solution.         |
|Stockage de données pour site de récupération d’urgence sur Azure VMware Solution : répliquer des données à partir d’un environnement VMware local ou principal vers le pool de disques en tant que site secondaire.     |SSD Premium         |Utiliser la passerelle de réseau virtuel ExpressRoute Standard (1 Gbits/s) ou Haute performance (2 Gbits/s) pour connecter le réseau virtuel du pool de disques au cloud Azure VMware Solution.         |

Consultez [Liste de vérification pour la planification réseau pour Azure VMware Solution](../azure-vmware/tutorial-network-checklist.md) pour planifier votre configuration réseau, ainsi que découvrir d’autres considérations relatives à Azure VMware Solution.

## <a name="disk-pool-scalability-and-performance-targets"></a>Objectifs de performance et d’extensibilité de pool de disques

|Ressource  |Limite  |
|---------|---------|
|Nombre maximal de disques par pool|8|
|Nombre maximal d’IOPS par pool de disques|25 600|
|Nombre maximal de Mbits/s par pool de disques|384|

L’exemple suivant doit vous donner une idée de la façon dont les différents facteurs de performance fonctionnent ensemble :

Par exemple, si vous ajoutez 2 disques SSD Premium de 1 Tio (P30, avec cible approvisionnée de 5 000 IOPS et 200 Mbits/s)  dans un pool de disques, vous obtenez 2 x 5000 = 10 000 IOPS. Toutefois, le débit est plafonné à 384 Mbits/s par pool de disques. Pour aller au-delà de cette limite de 384 Mbits/s, vous pouvez déployer davantage de pools de disques pour effectuer un scale-out afin d’augmenter débit. Le débit de votre réseau limite l’efficacité du scale-out.

## <a name="availability"></a>Disponibilité

Les pools de disques étant actuellement en préversion, ils ne doivent pas être utilisés pour des charges de travail de production.

Si votre pool de disques devient inaccessible à votre cloud Azure VMware Solution pour une raison quelconque, les conséquences seront les suivantes :

- Tous les magasins de données associés au pool de disques cesseront d’être accessibles.
- Toutes les machines virtuelles VMware hébergées dans ce cloud Azure VMware Solution qui utilisent les magasins de données concernés seront dans un état non sain.
- L’intégrité des clusters dans ce cloud Azure VMware Solution ne sera pas affectée, à l’exception d’une opération : vous ne pourrez pas mettre un ordinateur hôte en mode maintenance. Azure VMware Solution gèrera cet échec et tentera une récupération en déconnectant les magasins de données concernés.

## <a name="next-steps"></a>Étapes suivantes

[Déployer un pool de disques](disks-pools-deploy.md).
