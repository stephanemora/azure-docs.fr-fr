---
title: Configuration et optimisation de machines virtuelles Azure des séries H et N avec InfiniBand
description: Découvrez plus d’informations sur la configuration et l’optimisation des machines virtuelles Série H et Série N avec InfiniBand pour HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 470d5efae68366b5cc96243bab4ebb8552771650
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600877"
---
# <a name="configure-and-optimize-vms"></a>Configurer et optimiser les machines virtuelles

Cet article présente des conseils sur la configuration et l’optimisation des machines virtuelles compatibles InfiniBand [série H](../../sizes-hpc.md) et [série N](../../sizes-gpu.md) pour le calcul haute performance.

## <a name="vm-images"></a>Images de machine virtuelle
Sur les machines virtuelles prenant en charge InfiniBand, certains pilotes sont nécessaires pour activer la fonction RDMA.
- Les [images de machine virtuelle CentOS-HPC](#centos-hpc-vm-images) de la place de marché sont préconfigurées avec les pilotes IB appropriés et constituent le moyen le plus simple de commencer.
- Les [images de machine virtuelle Ubuntu](#ubuntu-vm-images) peuvent être configurées avec les bons pilotes IB. Il est recommandé de créer des [images de machine virtuelle personnalisées](../../linux/tutorial-custom-images.md) avec les pilotes et la configuration appropriés et de les réutiliser.

Sur les machines virtuelles de la [série N](../../sizes-gpu.md) avec GPU, les pilotes graphiques appropriés sont également requis et peuvent être ajoutés via les [extensions de machine virtuelle](../../extensions/hpccompute-gpu-linux.md) ou [manuellement](../../linux/n-series-driver-setup.md). Certaines images de machine virtuelle sur la place de marché sont également préinstallées avec les pilotes GPU Nvidia, dont certaines images de machine virtuelle de Nvidia.

### <a name="centos-hpc-vm-images"></a>Images de machine virtuelle CentOS-HPC

#### <a name="sr-iov-enabled-vms"></a>Machines virtuelles compatibles SR-IOV
Pour les [machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances), des [images de machine virtuelle CentOS-HPC sur la Place de marché](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) versions 7.6 et ultérieures conviennent. Ces images de machine virtuelle sont optimisées et préchargées avec les pilotes OFED pour RDMA ainsi que les diverses bibliothèques MPI et packages de calcul scientifique couramment utilisés. elles constituent le moyen le plus simple de commencer.
- Pour plus d’informations sur les éléments inclus dans les images de machine virtuelle CentOS-HPC version 7.6 et versions ultérieures, consultez cet [article TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557).
- Des scripts utilisés lors de la création des images de machine virtuelle CentOS-HPC version 7.6 et ultérieures à partir d’une image de base CentOS du Marketplace se trouvent sur le [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
  
> [!NOTE] 
> Les images de la Place de marché Azure HPC les plus récentes ont des cartes Mellanox OFED 5.1 et ultérieures, qui ne prennent pas en charge les cartes InfiniBand ConnectX3-Pro. Les tailles de machines virtuelles de série N compatibles SR-IOV avec FDR InfiniBand (p. ex. NCv3 et versions antérieures) peuvent utiliser les versions suivantes d’image de machine virtuelle CentOS-HPC et leurs versions antérieures sur la Place de marché :
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>Machines virtuelles non compatibles SR-IOV
Pour [les machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) sans SR-IOV, CentOS-HPC version 6.5 ou une version ultérieure, jusqu’à la 7.4 dans la place de marché, sont compatibles. Par exemple, pour les [machines virtuelles de la série H16](../../h-series.md), les versions 7,1 à 7,4 sont recommandées. Ces images de machine virtuelle sont préchargées avec les pilotes Network direct pour RDMA et Intel MPI version 5,1.

> [!NOTE]
> Sur ces images HPC basées sur CentOS pour les machines virtuelles non compatibles SR-IOV, les mises à jour du noyau sont désactivées dans le fichier de configuration **yum** . Cela s’explique par le fait que les pilotes RDMA NetworkDirect Linux sont distribués sous forme de package RPM, et que les mises à jour du pilote peuvent ne pas fonctionner si le noyau est mis à jour.

### <a name="rhelcentos-vm-images"></a>Images de machine virtuelle RHEL/CentOS
Les images de machine virtuelle non HPC basées sur RHEL ou CentOS sur la place de marché peuvent être configurées pour une utilisation sur [les machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) avec SR-IOV. En savoir plus sur [l’activation d’InfiniBand](enable-infiniband.md) et la [configuration des MPI](setup-mpi.md) sur les machines virtuelles.
- Des scripts utilisés lors de la création des images de machine virtuelle CentOS-HPC version 7.6 et ultérieures à partir d’une image de base CentOS du Marketplace se trouvant sur le [référentiel azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos) peuvent également être utilisés.
  
> [!NOTE]
> Mellanox OFED 5.1 et versions ultérieures ne prennent pas en charge les cartes InfiniBand ConnectX3-Pro sur les machines virtuelles de série N compatibles SR-IOV avec FDR InfiniBand (p. ex. NCv3). Utilisez la version LTS de Mellanox OFED 4.9-0.1.7.0 ou une version ultérieure sur les machines virtuelles de la série N avec des cartes ConnectX3-Pro. Vous trouverez plus de détails [ici](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Images de machine virtuelle Ubuntu
Les images de machine virtuelle Ubuntu Server 16.04 LTS, 18.04 LTS et 20.04 LTS dans la place de marché sont prises en charge par [les machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) avec et sans SR-IOV. En savoir plus sur [l’activation d’InfiniBand](enable-infiniband.md) et la [configuration dee MPI](setup-mpi.md) sur les machines virtuelles.
- Les instructions pour activer InfiniBand sur des images de machine virtuelle Ubuntu se trouvent dans cet [article TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).
- Les scripts utilisés lors de la création d’images de machine virtuelle HPC Ubuntu 18.04 et 20.04 basées sur LTS à partir d’une image de base de la Place de marché Ubuntu se trouvent sur le [référentiel azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu).

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
- Découvrez-en davantage sur l’installation et l’exécution des [différentes bibliothèques MPI prises en charge](setup-mpi.md) sur les machines virtuelles.
- Consultez la [Présentation de la série HBv3](hbv3-series-overview.md) et la [Présentation de la série HC](hc-series-overview.md).
- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
