---
title: Configuration et optimisation de machines virtuelles Azure des séries H et N avec InfiniBand
description: Découvrez plus d’informations sur la configuration et l’optimisation des machines virtuelles Série H et Série N avec InfiniBand pour HPC.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/07/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d4661c0819d214a2c750eb1582559f8d8a5959ed
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006602"
---
# <a name="configure-and-optimize-vms"></a>Configurer et optimiser les machines virtuelles

Cet article partage des techniques connues permettant de configurer et d’optimiser les machines virtuelles [Série H](../../sizes-hpc.md) et [Série N](../../sizes-gpu.md) avec InfiniBand pour HPC.

## <a name="vm-images"></a>Images de machine virtuelle
Sur les machines virtuelles prenant en charge InfiniBand, certains pilotes sont nécessaires pour activer la fonction RDMA. Sur Linux, les images de machine virtuelle CentOS-HPC de la Place de marché sont préconfigurées avec les pilotes appropriés. Les images de machine virtuelle Ubuntu peuvent être configurées avec les bons pilotes suivant ces [instructions](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351). Il est également recommandé de créer des [images de machine virtuelle personnalisées](../../linux/tutorial-custom-images.md) avec les pilotes et la configuration appropriés et de les réutiliser.

> [!NOTE]
> Sur les machines virtuelles de la [série N](../../sizes-gpu.md) avec GPU, les pilotes graphiques appropriés sont également requis et peuvent être ajoutés via les [extensions de machine virtuelle](../../extensions/hpccompute-gpu-linux.md) ou [manuellement](../../linux/n-series-driver-setup.md). Certaines images de machine virtuelle sur la place de marché sont également préinstallées avec les pilotes GPU NVIDIA.

### <a name="centos-hpc-vm-images"></a>Images de machine virtuelle CentOS-HPC

#### <a name="non-sr-iov-enabled-vms"></a>Machines virtuelles non compatibles SR-IOV
Pour [les machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) sans SR-IOV, CentOS-HPC version 6.5 ou une version ultérieure, jusqu’à la 7.5 dans la place de marché, sont compatibles. Par exemple, pour les [machines virtuelles de la série H16](../../h-series.md), les versions 7,1 à 7,5 sont recommandées. Ces images de machine virtuelle sont préchargées avec les pilotes Network direct pour RDMA et Intel MPI version 5,1.

> [!NOTE]
> Sur ces images HPC basées sur CentOS pour les machines virtuelles non compatibles SR-IOV, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum** . Cela s’explique par le fait que les pilotes RDMA NetworkDirect Linux sont distribués sous forme de package RPM, et que les mises à jour du pilote peuvent ne pas fonctionner si le noyau est mis à jour.

#### <a name="sr-iov-enabled-vms"></a>Machines virtuelles compatibles SR-IOV
  Pour [les machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) sans SR-IOV, [CentOS-HPC version 7.6 ou une version ultérieure](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), dans la place de marché, sont compatibles. Ces images de machine virtuelle sont optimisées et préchargées avec les pilotes OFED pour RDMA ainsi que les diverses bibliothèques MPI et packages de calcul scientifique couramment utilisés. elles constituent le moyen le plus simple de commencer.

  Exemples de scripts utilisés lors de la création des images de machine virtuelle CentOS-HPC version 7,6 et ultérieures à partir d’une image de base CentOS du Marketplace se trouvent sur le [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).

### <a name="rhelcentos-vm-images"></a>Images de machine virtuelle RHEL/CentOS
Les images de machine virtuelle non HPC basées sur RHEL ou CentOS sur la place de marché peuvent être configurées pour une utilisation sur [les machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) avec SR-IOV. En savoir plus sur [l’activation d’InfiniBand](enable-infiniband.md) et la [configuration des MPI](setup-mpi.md) sur les machines virtuelles.

  Exemples de scripts utilisés lors de la création des images de machine virtuelle CentOS-HPC version 7.6 et ultérieures à partir d’une image de base CentOS du Marketplace se trouvent sur le [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).

### <a name="ubuntu-vm-images"></a>Images de machine virtuelle Ubuntu
Les images de machine virtuelle Ubuntu Server 16.04 LTS, 18.04 LTS et 20.04 LTS dans la place de marché sont prises en charge par [les machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) avec et sans SR-IOV. En savoir plus sur [l’activation d’InfiniBand](enable-infiniband.md) et la [configuration dee MPI](setup-mpi.md) sur les machines virtuelles.

  Des exemples de scripts qui peuvent être utilisés lors de la création d’images de machines virtuelles HPC 18.04 LTS basées sur Ubuntu se trouvent sur le [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc).

### <a name="suse-linux-enterprise-server-vm-images"></a>Images de machine virtuelle SUSE Linux Enterprise Server
Les images de machine virtuelle SLES 12 SP3 pour HPC, SLES 12 SP3 pour HPC (Premium), SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium), SLES 12 SP4 et SLES 15 dans la place de marché sont pris en charge. Ces images de machine virtuelle sont préchargées avec les pilotes Network direct pour RDMA et Intel MPI version 5.1. En savoir plus sur la [configuration des MPI](setup-mpi.md) sur les machines virtuelles.

## <a name="optimize-vms"></a>Optimiser les machines virtuelles

Voici quelques paramètres d’optimisation facultatifs pour améliorer les performances de la machine virtuelle.

### <a name="update-lis"></a>Mettre à jour LIS

Si nécessaire pour des fonctionnalités ou des performances, [les pilotes Solaris Integration Services (LIS)](../../linux/endorsed-distros.md) peuvent être installés ou mis à jour sur des distributions de système d’exploitation pris en charge, en particulier le déploiement à l’aide d’une image personnalisée ou d’une version de système d’exploitation plus ancienne telle que CentOS/RHEL 6.x ou une version antérieure de 7.x.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Libérer de la mémoire

Améliorez l’efficacité en libérant automatiquement de la mémoire pour éviter tout accès à distance à la mémoire.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Pour faire persister ceci après le redémarrage de la machine virtuelle :

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Désactiver le pare-feu et SELinux

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Désactiver cpupower

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Configurer WALinuxAgent

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
WALinuxAgent peut éventuellement être désactivé en tant qu’étape de pré-tâche et activé après la tâche pour une disponibilité de ressource de machine virtuelle maximale pour la charge de travail HPC.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’activation de InfiniBand](enable-infiniband.md) sur les machines virtuelles compatibles avec InfiniBand de[série H](../../sizes-hpc.md) et de [série N](../../sizes-gpu.md).
- Découvrez-en davantage sur l’installation des [différentes bibliothèques MPI prises en charge](setup-mpi.md) et leur configuration optimale sur les machines virtuelles.
- Consultez [Vue d’ensemble de la série HB](hb-series-overview.md) et [Vue d’ensemble de la série HC](hc-series-overview.md) pour en savoir plus sur la configuration optimale des charges de travail pour les performances et la scalabilité.
- Découvrez des informations sur les dernières annonces et des exemples et des résultats HPC sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
