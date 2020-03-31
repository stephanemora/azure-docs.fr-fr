---
title: Activer InifinBand avec SR-IOV - Machines virtuelles Azure | Microsoft Docs
description: Découvrez comment activer InfiniBand avec SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/17/2019
ms.author: amverma
ms.openlocfilehash: 7f7907482da886d9da17ef1e7844b205f3e4b906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196766"
---
# <a name="enable-infiniband-with-sr-iov"></a>Activer InfiniBand avec SR-IOV

Les séries NC, ND et H de machines virtuelles Azure sont toutes associées à un réseau InfiniBand dédié. Toutes les tailles compatibles RDMA peuvent tirer parti de ce réseau à l’aide d’Intel MPI. Certaines séries de machines virtuelles ont une prise en charge étendue à toutes les implémentations MPI et verbes RDMA via SR-IOV. Les machines virtuelles compatibles RDMA incluent les machines virtuelles [optimisées pour le GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu) et [de calcul haute performance (HPC)](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

## <a name="choose-your-installation-path"></a>Choisir votre chemin d’installation

Pour commencer, l’option la plus simple consiste à utiliser une image de plateforme préconfigurée pour InfiniBand, le cas échéant :

- **Machines virtuelles IaaS HPC** : pour prendre en main les machines virtuelles IaaS pour HPC, la solution la plus simple consiste à utiliser [l’image de système d’exploitation de machine virtuelle CentOS-HPC 7.6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557), qui est déjà configurée avec InfiniBand. Étant donné que cette image est déjà configurée avec InfiniBand, vous n’êtes pas obligé de la configurer manuellement. Pour les versions Windows compatibles, consultez [Instances Windows prenant en charge RDMA](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

- **Machines virtuelles IaaS GPU** : aucune image de plateforme n’est actuellement préconfigurée pour les machines virtuelles optimisées pour le GPU, à l’exception de [l’image de système d’exploitation de machine virtuelle CentOS-HPC 7.6](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557). Pour configurer une image personnalisée avec InfiniBand, consultez [Installer manuellement Mellanox OFED](#manually-install-mellanox-ofed).

Si vous utilisez une image de machine virtuelle personnalisée ou une machine virtuelle [optimisée pour le GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu), vous devez la configurer avec InfiniBand en ajoutant l’extension de machine virtuelle InfiniBandDriverLinux ou InfiniBandDriverWindows à votre déploiement. Découvrez comment utiliser ces extensions de machine virtuelle avec [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) et [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances).

## <a name="manually-install-mellanox-ofed"></a>Installer manuellement Mellanox OFED

Pour configurer manuellement InfiniBand avec SR-IOV, procédez comme suit. L’exemple de cette procédure illustre la syntaxe RHEL/CentOS, mais les étapes sont générales et peuvent être utilisées pour tout système d’exploitation compatible, par exemple Ubuntu (16.04, 18.04, 19.04) et SLES (12 SP4 et 15). Les pilotes de la boîte de réception fonctionnent également, mais les pilotes Mellanox OpenFabrics fournissent davantage de fonctionnalités.

Pour plus d’informations sur les distributions prises en charge pour le pilote Mellanox, consultez les derniers [Pilotes Mellanox OpenFabrics](https://www.mellanox.com/page/products_dyn?product_family=26). Pour plus d’informations sur le pilote Mellanox OpenFabrics, reportez-vous au [Guide de l’utilisateur Mellanox](https://docs.mellanox.com/category/mlnxofedib).

Consultez l’exemple suivant pour savoir comment configurer InfiniBand sur Linux :

```bash
# Modify the variable to desired Mellanox OFED version
MOFED_VERSION=#4.7-1.0.0.1
# Modify the variable to desired OS
MOFED_OS=#rhel7.6
pushd /tmp
curl -fSsL https://www.mellanox.com/downloads/ofed/MLNX_OFED-${MOFED_VERSION}/MLNX_OFED_LINUX-${MOFED_VERSION}-${MOFED_OS}-x86_64.tgz | tar -zxpf -
cd MLNX_OFED_LINUX-*
sudo ./mlnxofedinstall
popd
```

Pour Windows, téléchargez et installez les [pilotes Mellanox OFED pour Windows](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34).

## <a name="enable-ip-over-infiniband"></a>Activer IP sur InfiniBand

Utilisez les commandes suivantes pour activer IP sur InfiniBand.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) sur Azure.
