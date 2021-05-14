---
title: Résolution des problèmes connus avec les machines virtuelles HPC et GPU - Machines virtuelles Azure | Microsoft Docs
description: Découvrez comment résoudre les problèmes connus liés aux tailles des machines virtuelles HPC et GPU dans Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/28/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7f9a10aca24203b69ff38ff5fab7960681145af5
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227827"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Problèmes connus avec les machines virtuelles des séries H et N

Cet article tente de répertorier les problèmes courants récents et leurs solutions correspondantes en lien avec l’utilisation de machines virtuelles HPC et GPU des séries [H](../../sizes-hpc.md) et [N](../../sizes-gpu.md).

## <a name="cache-topology-on-standard_hb120rs_v3"></a>Topologie du cache sur Standard_HB120rs_v3
`lstopo` affiche une topologie de cache incorrecte sur la taille de machine virtuelle Standard_HB120rs_v3. Il peut afficher qu’il n’y a que 32 Mo de L3 par NUMA. Toutefois, dans la pratique, il existe en fait 120 Mo de L3 par NUMA, comme prévu, car les mêmes 480 Mo de L3 sont disponibles pour l’ensemble de la machine virtuelle, comme pour les autres tailles de machine virtuelle HBv3 avec nombre de cœurs limité. Il s’agit d’une erreur superficielle dans l’affichage de la valeur correcte qui ne devrait pas avoir d’impact sur les charges de travail.

## <a name="qp0-access-restriction"></a>Restriction d’accès qp0
Pour empêcher l’accès au matériel de bas niveau pouvant entraîner des failles de sécurité, Queue Pair 0 n’est pas accessible aux machines virtuelles invitées. Cela ne doit affecter que les actions généralement associées à l’administration de la carte réseau ConnectX InfiniBand et l’exécution de diagnostics InfiniBand comme ibdiagnet, mais pas les applications de l’utilisateur final.

## <a name="mofed-installation-on-ubuntu"></a>Installation de MOFED sur Ubuntu
Sur les images de machine virtuelle de la Place de marché Ubuntu-18.04 avec la version des noyaux `5.4.0-1039-azure #42` et ultérieures, certains Mellanox OFED plus anciens ne sont pas compatibles, ce qui entraîne une augmentation du temps de démarrage de la machine virtuelle de jusqu’à 30 minutes dans certains cas. Cela a été signalé pour les versions d’OFED Mellanox 5.2-1.0.4.0 et 5.2-2.2.0.0. Le problème est résolu avec Mellanox OFED 5.3-1.0.0.1.
S’il est nécessaire d’utiliser l’OFED incompatible, une solution est d’utiliser l’image de machine virtuelle de la Place de marché **Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** ou antérieure, et de ne pas mettre à jour le noyau.

## <a name="mpi-qp-creation-errors"></a>Erreurs de création MPI QP
Si, lors de l’exécution de charges de travail MPI, les erreurs de création InfiniBand QP, comme illustré ci-dessous, sont levées, nous vous suggérons de redémarrer la machine virtuelle et de réessayer la charge de travail. Ce problème sera corrigé ultérieurement.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

Vous pouvez vérifier les valeurs du nombre maximal de paires de files d’attente lorsque le problème est observé comme suit.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Performances réseau accélérées sur HB, HC, HBv2 et NDv2

[Les performances réseau accélérées Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) sont désormais disponibles sur les tailles des machines virtuelles compatibles RDMA et InfiniBand et avec SR-IOV [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md) et [NDv2](../../ndv2-series.md). Cette capacité permet désormais d’améliorer le débit (jusqu’à 30 Gbits/s) et les latences sur le réseau Ethernet Azure. Bien qu’elle soit distincte des capacités RDMA sur le réseau InfiniBand, certaines modifications de plateforme pour cette capacité peuvent avoir un impact sur le comportement de certaines implémentations MPI lors de l’exécution de travaux sur InfiniBand. Plus précisément, l’interface InfiniBand sur certaines machines virtuelles peut avoir un nom légèrement différent (mlx5_1 par opposition à l’ancien mlx5_0), ce qui peut nécessiter la modification des lignes de commande MPI, en particulier lors de l’utilisation de l’interface UCX (généralement avec OpenMPI et HPC-X). La solution la plus simple consiste à utiliser le kit d’outils logiciels HPC-X le plus récent sur les images de machine virtuelle CentOS-HPC, ou à désactiver les performances réseau accélérées si elles ne sont pas nécessaires.
Pour plus de détails à ce sujet, consultez cet [article de la TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) qui contient des instructions sur la façon de traiter les problèmes observés.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>Installation du pilote InfiniBand sur des machines virtuelles non compatibles avec SR-IOV

Actuellement les machines virtuelles H16r, H16mr et NC24r ne sont pas compatibles avec SR-IOV. Pour plus détails sur la bifurcation de la pile InfiniBand, voir [ici](../../sizes-hpc.md#rdma-capable-instances).
InfiniBand peut être configuré sur des machines virtuelles compatibles avec SR-IOV à l’aide de pilotes OFED, tandis que les tailles machines virtuelles non compatibles avec SR-IOV requièrent des pilotes ND. Cette prise en charge d’IB est disponible de manière appropriée pour [CentOS, RHEL et Ubuntu](configure.md).

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Adresse MAC en doublon avec cloud-init avec Ubuntu sur des machines virtuelles des séries H et N

Il existe un problème connu avec cloud-init sur les images de machine virtuelle Ubuntu quand il tente d’activer l’interface IB. Ceci peut se produire lors du redémarrage de la machine virtuelle ou lors de la tentative de création d’une image de machine virtuelle après la généralisation. Les journaux de démarrage de la machine virtuelle peuvent indiquer une erreur de ce type :
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Ce problème « Adresse MAC en doublon avec cloud-init sur Ubuntu » est connu. Il sera résolu dans les noyaux plus récents. Si vous rencontrez ce problème, la solution de contournement est la suivante :
1) Déployer l’image de machine virtuelle de la Place de marché (Ubuntu 18.04)
2) Installer les packages logiciels nécessaires pour activer IB ([les instructions sont ici](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Éditer le fichier waagent.conf et changer EnableRDMA=y
4) Désactiver le réseau dans cloud-init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Éditer le fichier de configuration réseau de netplan généré par cloud-init pour supprimer l’adresse MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>DRAM sur machines virtuelles de série HB

Les machines virtuelles de série HB peuvent exposer seulement 228 Go de RAM aux machines virtuelles invitées pour le moment. De même, 458 Go sur des machines virtuelles HBv2 et 448 Go sur des machines virtuelles HBv3. Cela est dû à une limitation connue de l’hyperviseur Azure pour empêcher que des pages soient affectées à la DRAM locale d’AMD CCX (domaines NUMA) réservée à la machine virtuelle invitée.

## <a name="gss-proxy"></a>GSS Proxy

GSS Proxy a un problème connu dans CentOS/RHEL 7.5 qui peut se manifester sous la forme d’une réduction significative des performances et de la réactivité lorsqu’il est utilisé avec NFS. Comment atténuer cela :

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Nettoyage du cache

Sur les systèmes HPC, il est souvent utile de nettoyer la mémoire une fois qu’une tâche est terminée, avant que l’utilisateur suivant soit affecté au même nœud. Après l’exécution d’applications dans Linux, vous pouvez constater que la mémoire disponible se réduit alors que votre mémoire tampon augmente, alors que vous n’exécutez aucune application.

![Capture d’écran de l’invite de commandes avant le nettoyage](./media/known-issues/cache-cleaning-1.png)

Utiliser `numactl -H` affichera les NUMAnodes mis en mémoire tampon (potentiellement tous). Sous Linux, les utilisateurs peuvent nettoyer les caches de trois façons pour « libérer » la mémoire tampon ou en cache. Vous devez disposer des autorisations de sudo ou root.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Capture d’écran de l’invite de commandes après le nettoyage](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avertissements du noyau

Vous pouvez ignorer les messages d’avertissement liés au noyau suivants lors du démarrage d’une machine virtuelle de la série HB sous Linux. Il s’agit d’une limitation connue de l’hyperviseur Azure, qui sera corrigée en temps voulu.

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


## <a name="next-steps"></a>Étapes suivantes

- Consultez [Vue d’ensemble de la série HB](hb-series-overview.md) et [Vue d’ensemble de la série HC](hc-series-overview.md) pour en savoir plus sur la configuration optimale des charges de travail pour les performances et la scalabilité.
- Consultez les dernières annonces, des exemples de charge de travail HPC et les résultats des performances sur les [blogs de la communauté Azure Compute Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Pour une vision plus globale de l’architecture d’exécution des charges de travail HPC, consultez [Calcul haute performance (HPC) sur Azure](/azure/architecture/topics/high-performance-computing/).
