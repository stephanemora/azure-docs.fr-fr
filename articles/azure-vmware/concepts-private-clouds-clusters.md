---
title: Concepts – Clusters et clouds privés
description: Découvrez les principales fonctionnalités des centres de données à définition logicielle Azure VMware et des clusters vSphere dans la Solution VMware sur Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 09e1fd45b1dd873509f942ef8b524783acfed4ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906987"
---
# <a name="azure-vmware-solution-avs-preview-private-cloud-and-cluster-concepts"></a>Concepts de cloud privé et de cluster de la solution VMware sur Azure (AVS) en préversion

La solution VMware sur Azure (AVS) fournit des clouds privés basés sur VMware dans Azure. Les clouds privés sont créés à partir de clusters d’hôtes nus dédiés, et sont déployés et gérés via le portail Azure. Les clusters dans des clouds privés sont approvisionnés avec les logiciels VMware vSphere, vCenter, vSAN et NSX. Les déploiements de logiciels et composants matériels de cloud privé AVS sont entièrement intégrés et automatisés dans Azure.

Il existe une relation logique entre les abonnements Azure, les clouds privés AVS, les clusters vSAN et les hôtes. Le diagramme présente deux clouds privés dans un seul abonnement Azure. Les clouds privés représentent un environnement de développement et un environnement de production, chacun avec son propre cloud privé. Chaque cloud privé comprend deux clusters. Pour refléter les besoins potentiels moindres d’un environnement de développement, des clusters plus petits avec des hôtes de capacité inférieure sont utilisés. Tous ces concepts sont décrits dans les sections ci-dessous.

![Image de deux clouds privés dans un abonnement client](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Clouds privés

Les clouds privés contiennent des clusters vSAN qui sont créés avec des hôtes Azure dédiés nus. Chaque cloud privé peut avoir plusieurs clusters, tous gérés par les mêmes serveur vCenter et gestionnaire NSX-T. Vous pouvez déployer et gérer des clouds privés dans le portail à partir de l’interface de ligne de commande ou à l’aide de PowerShell. Comme avec d’autres ressources, les clouds privés sont installés et gérés à partir d’un abonnement Azure.

Le nombre de clouds privés au sein d’un abonnement est évolutif. Au départ, il existe une limite d’un cloud privé par abonnement.

## <a name="clusters"></a>Clusters

Vous allez créer au moins un cluster vSAN dans chaque cloud privé. Lorsque vous créez un cloud privé, celui-ci comprend un cluster par défaut. Vous pouvez ajouter des clusters à un cloud privé via le portail Azure ou l’API. Tous les clusters ont une taille par défaut de trois hôtes et peuvent être mis à l’échelle pour inclure de 3 à 16 hôtes. Les hôtes utilisés dans un cluster doivent être du même type. Les types d’hôtes sont décrits dans la section suivante.

Des clusters d’évaluation sont disponibles. Ils sont limités à trois hôtes et à un cluster d’évaluation par cloud privé. Vous pouvez mettre à l’échelle un cluster d’évaluation à l’aide d’un seul hôte au cours de la période d’évaluation.

Vous créez, supprimez et mettez à l’échelle les clusters via le portail ou l’API. Vous utilisez toujours le serveur vSphere et le gestionnaire NSX-T pour gérer la plupart des autres aspects de la configuration ou de l’exploitation du cluster. Tout le stockage local de chaque hôte dans un cluster est contrôlé par vSAN.

## <a name="hosts"></a>Hôtes

Des nœuds d’infrastructure hyper-convergés et nus sont utilisés dans les clusters de cloud privé AVS. Les capacités de mémoire RAM, de processeur et de disque de l’hôte sont indiquées dans le tableau ci-dessous. 

| Type d’hôte              |             UC             |   RAM (Go)   |  Niveau de cache du vSAN NVMe (To, RAW)  |  Niveau de capacité du vSAN SSD (To, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| Haut de gamme (HE)          |  Deux processeurs Intel 18 cœurs cadencés à 2,3 GHz  |     576      |                3.2               |                15,20               |

Les hôtes utilisés pour créer ou mettre à l’échelle des clusters sont acquis à partir d’un pool isolé d’hôtes. Ces hôtes ont passé des tests matériels et toutes les données ont été effacées des disques Flash en toute sécurité. Lorsque vous supprimez un hôte d’un cluster, les disques internes sont effacés en toute sécurité et l’hôte est placé dans le pool isolé d’hôtes. Lorsque vous ajoutez un hôte à un cluster, un hôte assaini du pool isolé est utilisé.

## <a name="vmware-software-versions"></a>Versions des logiciels VMware

Les versions actuelles des logiciels VMware utilisés dans des clusters de cloud privé AVS sont les suivantes :

| Logiciel              |    Version   |
| :---                  |     :---:    |
| VCSA / vSphere / ESXi |    6,7 U2    | 
| ESXi                  |    6,7 U2    | 
| vSAN                  |    6,7 U2    |
| NSX-T                 |      2.5     |

Pour tout nouveau cluster dans un cloud privé, la version du logiciel correspond à ce qui est actuellement en cours d’exécution dans le cloud privé. Pour tout nouveau cloud privé dans un abonnement client, la version la plus récente de la pile logicielle est installée.

Les stratégies et processus généraux de mise à niveau pour le logiciel de plateforme AVS sont décrits dans le document sur les concepts de mise à niveau.

## <a name="host-maintenance-and-lifecycle-management"></a>Maintenance de l’hôte et gestion du cycle de vie

La maintenance de l’hôte et la gestion du cycle de vie sont effectuées sans impact sur la capacité ou les performances des clusters du cloud privé. Les mises à niveau de microprogramme et la réparation ou le remplacement de matériel sont des exemples de maintenance automatisée de l’hôte.

Microsoft est responsable de la gestion du cycle de vie des appliances NSX-T telles que le gestionnaire et la périphérie NSX-T. Microsoft est également responsable de l’amorçage de la configuration réseau, par exemple, la création de la passerelle de niveau 0 et l’activation du routage Nord-Sud. En tant qu’administrateur de votre cloud privé AVS, vous êtes responsable de la configuration du SDN NSX-T, par exemple, des segments réseau, des règles de pare-feu distribuées, des passerelles de niveau 1 et des équilibreurs de charge.

> [!IMPORTANT]
> L’administrateur AVS ne doit pas modifier la configuration de la périphérie NSX-T ou de la passerelle de niveau 0. Cela peut entraîner une perte de service.

## <a name="backup-and-restoration"></a>Sauvegarde et restauration

Les configurations du serveur vCenter et du gestionnaire NSX-T du cloud privé sont sauvegardées toutes les heures. Les sauvegardes sont conservées pendant trois jours. La restauration d’une sauvegarde est demandée via une demande de service dans le portail Azure.

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste à découvrir les [concepts de mise en réseau et d’interconnexion](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

