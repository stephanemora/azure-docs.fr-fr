---
title: Activer InifinBand avec SR-IOV - Machines virtuelles Azure | Microsoft Docs
description: Découvrez comment activer InfiniBand avec SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 81acb804ed2ebb9e88bc7d8281a7fa52359d4455
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66810081"
---
# <a name="enable-infiniband-with-sr-iov"></a>Activer InfiniBand avec SR-IOV


La façon la plus simple et recommandée de configurer votre image de machine virtuelle personnalisée avec InfiniBand (IB) consiste à ajouter l’extension de machine virtuelle InfiniBandDriverLinux ou InfiniBandDriverWindows à votre déploiement.
Découvrez comment utiliser ces extensions de machine virtuelle avec [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) et [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Pour configurer manuellement InfiniBand sur des machines virtuelles compatibles SR-IOV (actuellement les séries HB et HC), suivez les étapes ci-dessous. Ces étapes concernent uniquement RHEL/CentOS. Pour Ubuntu (16.04 et 18.04) et SLES (12 SP4 et 15), les pilotes de la boîte de réception fonctionnent bien. Pour Ubuntu, 


## <a name="manually-install-ofed"></a>Installez manuellement OFED

Installez les derniers pilotes MLNX_OFED ConnectX-5 à partir de [Mellanox](http://www.mellanox.com/page/products_dyn?product_family=26).

Pour RHEL/CentOS (exemple ci-dessous pour 7.6) :
```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Pour Windows, téléchargez et installez les pilotes WinOF-2 pour ConnectX-5 à partir de [Mellanox](http://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="assign-an-ip-address"></a>Attribuer une adresse IP

Attribuer une adresse IP à l’interface ib0 à l’aide de :

- Attribuez manuellement une adresse IP à l’interface ib0 (en tant que racine).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

Ou

- Utilisez WALinuxAgent pour attribuer une adresse IP et la rendre persistante.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) sur Azure.
