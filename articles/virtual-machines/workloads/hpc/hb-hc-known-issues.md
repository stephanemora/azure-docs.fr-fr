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
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: e85ae50321b9aa034f6a6d2cadcc329a24dafa62
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500016"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>Problèmes connus avec les machines virtuelles des séries HC et HB

Cet article indique les problèmes les plus courants et les solutions correspondantes lors de l’utilisation de machines virtuelles de série HC et HB.

## <a name="dram-on-hb-series"></a>DRAM sur la série HB

Les machines virtuelles de série HB peuvent exposer seulement 228 Go de RAM aux machines virtuelles invitées pour le moment. Cela est dû à une limitation connue de l’hyperviseur Azure pour empêcher que des pages soient affectées à la DRAM locale d’AMD CCX (domaines NUMA) réservée à la machine virtuelle invitée.

## <a name="accelerated-networking"></a>Mise en réseau accélérée

La mise en réseau accélérée Azure n’est pas activée pour l’instant, mais elle le sera à mesure que nous avançons dans la période de préversion. Nous informerons les clients lorsque cette fonctionnalité sera prise en charge.

## <a name="qp0-access-restriction"></a>Restriction d’accès qp0

Pour empêcher l’accès au matériel de bas niveau pouvant entraîner des failles de sécurité, Queue Pair 0 n’est pas accessible aux machines virtuelles invitées. Cela ne doit affecter que les actions généralement associées à l’administration de la carte réseau ConnectX-5 et à l’exécution des diagnostics InfiniBand comme ibdiagnet, et pas les applications de l’utilisateur final elles-mêmes.

## <a name="ud-transport"></a>Transport UD

Au lancement, les séries HB et HC ne prennent pas en charge le transport connecté de façon dynamique (DCT). La prise en charge de DCT sera implémentée en temps voulu. Les transports de connexion fiable (RC) et de datagramme non fiable (UD) sont pris en charge.

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

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur le [calcul haute performance](/azure/architecture/topics/high-performance-computing/) dans Azure.
