---
title: Configuration et optimisation de machines virtuelles Azure des séries H et N avec InfiniBand
description: Découvrez plus d’informations sur la configuration et l’optimisation des machines virtuelles Série H et Série N avec InfiniBand pour HPC.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/28/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 486bb13b1603f239d04d8805020713aaf3a7333f
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227773"
---
# <a name="configure-and-optimize-vms"></a>Configurer et optimiser les machines virtuelles

Cet article donne des conseils sur la configuration et l’optimisation des machines virtuelles de la [série H](../../sizes-hpc.md) et de la [série N](../../sizes-gpu.md) avec InfiniBand pour le calcul haute performance.

## <a name="vm-images"></a>Images de machine virtuelle
Sur les machines virtuelles avec InfiniBand, certains pilotes sont nécessaires pour activer la fonction RDMA.
- Les [images de machine virtuelle CentOS-HPC](#centos-hpc-vm-images) de Place de marché sont préconfigurées avec les pilotes InfiniBand appropriés.
- Les [images de machine virtuelle Ubuntu-HPC](#ubuntu-hpc-vm-images) de Place de marché sont préconfigurées avec les pilotes InfiniBand et GPU appropriés.

Ces images de machine virtuelle ont été conçues à partir des images de machine virtuelle CentOS et Ubuntu de base du marketplace. Les scripts utilisés dans la création de ces images de machine virtuelle à partir de leur image CentOS de base de Place de marché se trouvent sur le [référentiel azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).

Sur les machines virtuelles de [série N](../../sizes-gpu.md) avec GPU, les pilotes GPU appropriés sont également nécessaires. Ils peuvent être obtenus avec les méthodes suivantes :
- Utilisez les [images de machine virtuelle Ubuntu-HPC](#ubuntu-hpc-vm-images) qui sont préconfigurées avec les pilotes GPU de Nvidia et la pile logicielle de calcul GPU (CUDA, NCCL).
- Ajoutez les pilotes GPU par le biais des [extensions de machine virtuelle](../../extensions/hpccompute-gpu-linux.md).
- Installez les pilotes GPU [manuellement](../../linux/n-series-driver-setup.md).
- Certaines autres images de machine virtuelle sur Place de marché sont également préinstallées avec les pilotes GPU de Nvidia, y compris certaines images de machine virtuelle de Nvidia.

En fonction des besoins des charges de travail en matière de distribution et de version Linux, les [images de machine virtuelle CentOS-HPC](#centos-hpc-vm-images) et les [images de machine virtuelle Ubuntu-HPC](#ubuntu-hpc-vm-images) dans Place de marché constituent le moyen le plus simple de prendre en main des charges de travail HPC et IA sur Azure.
Il est également recommandé de créer des [images de machine virtuelle personnalisées](../../linux/tutorial-custom-images.md) avec une personnalisation et une configuration spécifiques et de les réutiliser.

### <a name="vm-sizes-supported-by-the-hpc-vm-images"></a>Tailles de machines virtuelles prises en charge par les images de machine virtuelle HPC
Les images du marketplace HPC Azure les plus récentes sont fournies avec Mellanox OFED 5.1 et versions ultérieures, qui ne prennent pas en charge les cartes InfiniBand ConnectX3-Pro. Ces images de machine virtuelle prennent uniquement en charge les cartes InfiniBand ConnextX-5 et ultérieures. Cela implique la matrice suivante de prise en charge des tailles de machine virtuelle pour l’OFED InfiniBand dans ces images de machine virtuelle HPC :
- [Série H](../../sizes-hpc.md) : HB, HC, HBv2, HBv3
- [Série N](../../sizes-gpu.md) : NDv2, NDv4

Notez que pour la prise en charge du GPU dans les tailles de machine virtuelle de la série N (NDv2 et NDv4), seules les [images de machine virtuelle Ubuntu-HPC](#ubuntu-hpc-vm-images) sont actuellement préconfigurées avec les pilotes GPU de Nvidia et la pile logicielle de calcul GPU (CUDA, NCCL). 

Notez également que toutes les tailles de machine virtuelle ci-dessus prennent en charge les machines virtuelles « Gen 2 », bien que certaines plus anciennes prennent également en charge les machines virtuelles « Gen 1 ».

### <a name="centos-hpc-vm-images"></a>Images de machine virtuelle CentOS-HPC

#### <a name="sr-iov-enabled-vms"></a>Machines virtuelles compatibles SR-IOV
Pour les [machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances), des images de machine virtuelle CentOS-HPC versions 7.6 et ultérieures conviennent. Ces images de machine virtuelle sont optimisées et préchargées avec les pilotes Mellanox OFED pour RDMA, ainsi que les diverses bibliothèques MPI et différents packages de calcul scientifique couramment utilisés.
- Les versions disponibles ou les plus récentes des images de machine virtuelle peuvent être répertoriées avec les informations suivantes à l’aide de [CLI](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az_vm_image_list) ou de [Place de marché](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview).
   ```bash
   "publisher": "OpenLogic",
   "offer": "CentOS-HPC",
   ```
- Des scripts utilisés lors de la création des images de machine virtuelle CentOS-HPC version 7.6 et ultérieures à partir d’une image de base CentOS du Marketplace se trouvent sur le [repository azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos).
- Pour plus d’informations sur les éléments inclus dans les images de machine virtuelle CentOS-HPC version 7.6 et versions ultérieures et sur la façon de les déployer, consultez cet [article TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094).

> [!NOTE] 
> Les tailles de machines virtuelles de série N compatibles SR-IOV avec FDR InfiniBand (p. ex. NCv3 et versions antérieures) peuvent utiliser les versions suivantes d’image de machine virtuelle CentOS-HPC et leurs versions antérieures sur la Place de marché :
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

### <a name="ubuntu-hpc-vm-images"></a>Images de machine virtuelle Ubuntu-HPC
Pour les [machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) avec SR-IOV, des images de machine virtuelle Ubuntu-HPC versions 18.04 et ultérieures conviennent. Ces images de machine virtuelle sont optimisées et préchargées avec les pilotes Mellanox OFED pour RDMA, les pilotes GPU de Nvidia, la pile logicielle de calcul GPU (CUDA, NCCL) et les diverses bibliothèques MPI et les différents packages de calcul scientifique couramment utilisés.
- Les versions disponibles ou les plus récentes des images de machine virtuelle peuvent être répertoriées avec les informations suivantes à l’aide de [CLI](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az_vm_image_list) ou de [Place de marché](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-hpc?tab=overview).
   ```bash
   "publisher": "Microsoft-DSVM",
   "offer": "Ubuntu-HPC",
   ```
- Les scripts utilisés lors de la création d’images de machine virtuelle Ubuntu-HPC à partir d’une image Ubuntu de base de Place de marché se trouvent sur le [référentiel azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu).
- Pour plus d’informations sur les éléments inclus dans les images de machine virtuelle Ubuntu-HPC et sur la façon de les déployer, consultez cet [article TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094).

### <a name="rhelcentos-vm-images"></a>Images de machine virtuelle RHEL/CentOS
Les images de base de machine virtuelle non HPC basées sur RHEL ou CentOS sur Place de marché peuvent être configurées pour une utilisation sur les [machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) avec SR-IOV. En savoir plus sur [l’activation d’InfiniBand](enable-infiniband.md) et la [configuration des MPI](setup-mpi.md) sur les machines virtuelles.
- Des scripts utilisés lors de la création des images de machine virtuelle CentOS-HPC version 7.6 et ultérieures à partir d’une image de base CentOS du Marketplace se trouvant sur le [référentiel azhpc-images](https://github.com/Azure/azhpc-images/tree/master/centos) peuvent également être utilisés.
 
### <a name="ubuntu-vm-images"></a>Images de machine virtuelle Ubuntu
Les images de base de machine virtuelle Ubuntu Server 16.04 LTS, 18.04 LTS et 20.04 LTS dans Place de marché sont prises en charge par les [machines virtuelles compatibles RDMA](../../sizes-hpc.md#rdma-capable-instances) avec et sans SR-IOV. En savoir plus sur [l’activation d’InfiniBand](enable-infiniband.md) et la [configuration dee MPI](setup-mpi.md) sur les machines virtuelles.
- Les instructions pour activer InfiniBand sur des images de machine virtuelle Ubuntu se trouvent dans cet [article TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351).
- Les scripts utilisés lors de la création d’images de machine virtuelle HPC Ubuntu 18.04 et 20.04 basées sur LTS à partir d’une image de base de la Place de marché Ubuntu se trouvent sur le [référentiel azhpc-images](https://github.com/Azure/azhpc-images/tree/master/ubuntu).

> [!NOTE]
> Mellanox OFED 5.1 et versions ultérieures ne prennent pas en charge les cartes InfiniBand ConnectX3-Pro sur les machines virtuelles de série N compatibles SR-IOV avec FDR InfiniBand (p. ex. NCv3). Utilisez la version LTS de Mellanox OFED 4.9-0.1.7.0 ou une version ultérieure sur les machines virtuelles de la série N avec des cartes ConnectX3-Pro. Vous trouverez plus de détails [ici](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="suse-linux-enterprise-server-vm-images"></a>Images de machine virtuelle SUSE Linux Enterprise Server
Les images de machine virtuelle SLES 12 SP3 pour HPC, SLES 12 SP3 pour HPC (Premium), SLES 12 SP1 pour HPC, SLES 12 SP1 pour HPC (Premium), SLES 12 SP4 et SLES 15 dans la place de marché sont pris en charge. Ces images de machine virtuelle sont préchargées avec les pilotes Network Direct pour RDMA (sur les tailles de machine virtuelle sans SR-IOV) et Intel MPI version 5.1. En savoir plus sur la [configuration des MPI](setup-mpi.md) sur les machines virtuelles.

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
