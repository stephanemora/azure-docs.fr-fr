---
title: Vue d’ensemble des machines virtuelles de série HBv3 – Machines virtuelles Azure | Microsoft Docs
description: En savoir plus sur la taille des machines virtuelles de la série HBv3 dans Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d1abd03f517f9e0b13a2994418cbae5cfbe22454
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801862"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Vue d’ensemble des machines virtuelles de la série HBv3 

Un serveur de la [série HBv3](../../hbv3-series.md) comprend 2 processeurs EPYC 7V13 de 64 cœurs, soit un total de 128 cœurs « Zen3 » physiques. Le multithreading simultané (SMT) est désactivé sur HBv3. Ces 128 cœurs sont divisés en 16 sections (8 par socket), chaque section contenant 8 cœurs de processeur avec un accès uniforme à un cache L3 de 32 Mo. Les serveurs HBv3 Azure exécutent également les paramètres du BIOS AMD suivants :

```bash
Nodes per Socket =2
L3 as NUMA = Disabled
```

Par conséquent, le serveur démarre avec 4 domaines NUMA (2 par socket), d’une taille de 32 cœurs chacun. Chaque NUMA a un accès direct à 4 canaux de DRAM physique fonctionnant à 3200 MT/s.

Pour permettre à l’hyperviseur Azure de fonctionner sans interférer avec la machine virtuelle, nous réservons 8 cœurs physiques par serveur. 

Notez que les tailles de machine virtuelle avec cœurs limités réduisent uniquement le nombre de cœurs physiques exposés à la machine virtuelle. Toutes les ressources partagées globales (RAM, bande passante mémoire, cache L3, connectivité GMI et xGMI, InfiniBand, réseau Ethernet Azure, disque SSD local) restent constantes. Cela permet à un client de choisir la taille de machine virtuelle optimale pour un ensemble donné d’exigences en termes de charge de travail ou de licences logicielles.

Le diagramme suivant illustre la répartition des cœurs réservés pour l’hyperviseur Azure (en jaune) et la machine virtuelle HBv3 (en vert).
![Répartition des cœurs réservés pour l’hyperviseur Azure et la machine virtuelle HBv3](./media/architecture/hbv3-segregation-cores.png)

## <a name="infiniband-networking"></a>Mise en réseau InfiniBand
Les machines virtuelles HBv3 sont également dotées de cartes réseau InfiniBand HDR Nvidia Mellanox (ConnectX-6) fonctionnant jusqu’à 200 gigabits/s. La carte réseau est transmise à la machine virtuelle via SR-IOV, ce qui permet au trafic réseau de contourner l’hyperviseur. Par conséquent, les clients chargent des pilotes OFED Mellanox standard sur des machines virtuelles HBv3 de la même manière que sur un environnement matériel nu.

Les machines virtuelles HBv3 prennent en charge le routage adaptatif, le transport connecté dynamique (DCT) (en plus des transports standard RC et UD) et le déchargement matériel des collectifs MPI sur le processeur intégré de l’adaptateur ConnectX-6. Ces fonctionnalités améliorent les performances, l’extensibilité et la cohérence des applications, et leur utilisation est fortement recommandée.

## <a name="temporary-storage"></a>Stockage temporaire
Les machines virtuelles HBv3 sont dotées de 3 disques SSD physiques locaux. Un appareil est préformaté pour servir de fichier d’échange et apparaît dans votre machine virtuelle en tant que disque « SSD » générique.

Deux autres disques SSD de plus grande taille sont fournis en tant qu’appareils NVMe de bloc non formatés via NVMeDirect. Comme l’appareil NVMe de bloc contourne l’hyperviseur, il dispose d’une bande passante plus élevée, d’un nombre IOPS plus élevé et d’une latence inférieure par IOP.

Quand il est associé à une batterie agrégée par bandes, le disque SSD NVMe fournit jusqu’à 7 Go/s de lectures et 3 Go/s d’écritures, et jusqu’à 186 000 IOPS (lectures) et 201 000 IOPS (écritures) pour les profondeurs de file d’attente profondes.

## <a name="hardware-specifications"></a>Spécifications matérielles 

| Spécifications matérielles          | Machines virtuelles de série HBv3              |
|----------------------------------|----------------------------------|
| Cœurs                            | 120, 96, 64, 32 ou 16 (SMT désactivé)               | 
| UC                              | AMD EPYC 7V13                   | 
| Fréquence de l’UC (non AVX)          | 3,1 GHz (tous les cœurs), 3 675 GHz (jusqu’à 10 cœurs)    | 
| Mémoire                           | 448 Go (RAM par cœur en fonction de la taille de la machine virtuelle)         | 
| Disque local                       | 2 disques NVMe de 960 Go (bloc), disque SSD de 480 Go (fichier d’échange) | 
| Infiniband                       | Mellanox ConnectX-6 HDR InfiniBand 200 Gbits/s | 
| Réseau                          | Ethernet 50 Gbits (40 Gbits utilisables) SmartNIC Azure 2ème gén. | 

## <a name="software-specifications"></a>Spécifications logicielles 

| Spécifications logicielles        | Machines virtuelles de série HBv3                                            | 
|--------------------------------|-----------------------------------------------------------|
| Taille de travail MPI max               | 36 000 cœurs (300 machines virtuelles dans un seul groupe de machines virtuelles identiques avec singlePlacementGroup=true) |
| Prise en charge MPI                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| Frameworks supplémentaires          | UCX, libfabric, PGAS                  |
| Prise en charge de Stockage Azure          | Disques Standard et Premium (maximum 32 disques)              |
| Prise en charge du système d’exploitation pour SRIOV RDMA      | CentOS/RHEL 7.6+, Ubuntu 18.04+, SLES 12 SP4+, WinServer 2016+           |
| Système d’exploitation recommandé pour de meilleures performances | CentOS 8.1, Windows Server 2019+
| Prise en charge d’Orchestrator           | Azure CycleCloud, Azure Batch, AKS ; [options de configuration de cluster](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 R2 n’est pas pris en charge sur les HBv3 et autres machines virtuelles ayant plus de 64 cœurs (virtuels ou physiques). Consultez [ce document](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).