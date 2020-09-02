---
title: Problèmes connus avec les machines virtuelles des séries HC et HB - Machines virtuelles Microsoft Azure | Microsoft Docs
description: En savoir plus sur les problèmes connus avec les tailles de machines virtuelles de série HB dans Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6316bcc91bb381facb4f77b2d8dbd8b22f9ed387
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660093"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Problèmes connus avec les machines virtuelles des séries H et N

Cet article présente les problèmes les plus courants et les solutions correspondantes lors de l’utilisation de machines virtuelles de série [H](../../sizes-hpc.md) et [N](../../sizes-gpu.md).

## <a name="dram-on-hb-series"></a>DRAM sur la série HB

Les machines virtuelles de série HB peuvent exposer seulement 228 Go de RAM aux machines virtuelles invitées pour le moment. Cela est dû à une limitation connue de l’hyperviseur Azure pour empêcher que des pages soient affectées à la DRAM locale d’AMD CCX (domaines NUMA) réservée à la machine virtuelle invitée.

## <a name="accelerated-networking"></a>Mise en réseau accélérée

La mise en réseau accélérée Azure n’est pas activée pour l’instant, mais elle le sera à mesure que nous avançons dans la période de préversion. Nous informerons les clients lorsque cette fonctionnalité sera prise en charge.

## <a name="qp0-access-restriction"></a>Restriction d’accès qp0

Pour empêcher l’accès au matériel de bas niveau pouvant entraîner des failles de sécurité, Queue Pair 0 n’est pas accessible aux machines virtuelles invitées. Cela ne doit affecter que les actions généralement associées à l’administration de la carte réseau ConnectX-5 et à l’exécution des diagnostics InfiniBand comme ibdiagnet, et pas les applications de l’utilisateur final elles-mêmes.

## <a name="gss-proxy"></a>GSS Proxy

GSS Proxy a un problème connu dans CentOS/RHEL 7.5 qui peut se manifester sous la forme d’une réduction significative des performances et de la réactivité lorsqu’il est utilisé avec NFS. Comment atténuer cela :

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Nettoyage du cache

Sur les systèmes HPC, il est souvent utile de nettoyer la mémoire une fois qu’une tâche est terminée, avant que l’utilisateur suivant soit affecté au même nœud. Après l’exécution d’applications dans Linux, vous pouvez constater que la mémoire disponible se réduit alors que votre mémoire tampon augmente, alors que vous n’exécutez aucune application.

![Capture d’écran de l’invite de commande](./media/known-issues/cache-cleaning-1.png)

Utiliser `numactl -H` affichera les NUMAnodes mis en mémoire tampon (potentiellement tous). Sous Linux, les utilisateurs peuvent nettoyer les caches de trois façons pour « libérer » la mémoire tampon ou en cache. Vous devez disposer des autorisations de sudo ou root.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Capture d’écran de l’invite de commande](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avertissements du noyau

Vous pouvez voir les messages d’avertissement du noyau suivants lors du démarrage d’une machine virtuelle HB sous Linux.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Vous pouvez ignorer cet avertissement. Il s’agit d’une limitation connue de l’hyperviseur Azure, qui sera corrigée en temps voulu.


## <a name="infiniband-driver-installation-on-infiniband-enabled-n-series-vm-sizes"></a>Installation du pilote InfiniBand sur les tailles de machine virtuelle de série N compatibles avec InfiniBand

NC24r_v3 et ND40r_v2 sont compatibles avec SR-IOV alors que NC24r et NC24r_v2 ne le sont pas. Vous trouverez des détails sur la bifurcation [ici](../../sizes-hpc.md#rdma-capable-instances).
InfiniBand (IB) peut être configuré sur les tailles de machine virtuelle compatibles avec SR-IOV à l’aide de pilotes OFED tandis que les tailles de machine virtuelle non compatibles avec SR-IOV requièrent des pilotes ND. Cette prise en charge IB est disponible de manière appropriée sur [CentOS-HPC VMIs](configure.md). Pour Ubuntu, consultez les instructions disponibles [ici](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) pour installer les pilotes OFED et ND, comme décrit dans la [documentation](enable-infiniband.md#vm-images-with-infiniband-drivers).


## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble de la série HB](hb-series-overview.md) et [Vue d’ensemble de la série HC](hc-series-overview.md) pour en savoir plus sur la configuration optimale des charges de travail pour les performances et la scalabilité.
- Découvrez des informations sur les dernières annonces et des exemples et des résultats HPC sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
