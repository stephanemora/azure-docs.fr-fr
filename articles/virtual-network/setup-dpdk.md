---
title: DPDK dans une machine virtuelle Linux Azure | Microsoft Docs
description: Découvrez comment configurer DPDK dans une machine virtuelle Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: 205a1e399eadd268ffaa390a7ebb4397fda9feff
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444651"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>Configurer DPDK dans une machine virtuelle Linux

Le kit DPDK (Data Plan Development Kit) dans Azure offre une infrastructure de traitement des paquets d’espace utilisateur plus rapide pour les applications exigeantes en termes de performance qui contournent la pile réseau du noyau de la machine virtuelle.

Quand la pile réseau du noyau est utilisée, le traitement des paquets est généralement piloté par les interruptions. Chaque fois que l’interface réseau reçoit des paquets entrants, le noyau s’interrompt pour traiter le paquet et le changement de contexte de l’espace noyau à l’espace utilisateur. DPDK remplace le changement de contexte et la méthode pilotée par les interruptions par l’implémentation d’un espace utilisateur en utilisant les pilotes du mode d’interrogation pour un traitement rapide des paquets.

DPDK est constitué d’un ensemble de bibliothèques d’espace utilisateur qui donnent accès à des ressources de plus bas niveau, telles que le matériel, les cœurs logiques, la gestion de mémoire et les pilotes du mode d’interrogation pour les cartes d’interface réseau.

DPDK peut s’exécuter dans des machines virtuelles Azure, prenant en charge plusieurs distributions de système d’exploitation. DPDK se distingue sur le plan des performances quand il s’agit de piloter des implémentations de virtualisation de fonctions réseau à la faveur d’appliances virtuelles réseau (NVA), telles qu’un routeur virtuel, un pare-feu, un VPN, EPC (Evolved Packet Core) et des applications DDos (déni de service).

## <a name="benefit"></a>Avantage

**Plus de paquets traités à la seconde (PPS)**  : le contournement du noyau et la prise de contrôle des paquets dans l’espace utilisateur ont pour effet de réduire le nombre de cycles du fait de l’élimination du changement de contexte et d’améliorer le nombre de paquets traités à la seconde dans les machines virtuelles Linux Azure.


## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Voici les distributions de la galerie Azure qui sont prises en charge :

| Système d’exploitation Linux     | Version du noyau        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Prise en charge de noyau personnalisé**

Consultez [Patches for building an Azure-tuned Linux kernel](https://github.com/microsoft/azure-linux-kernel) pour toute version de noyau Linux non répertoriée ou contactez [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com) pour de plus amples informations. 

## <a name="region-support"></a>Prise en charge de la région

Toutes les régions Azure prennent en charge DPDK.

## <a name="prerequisites"></a>Prérequis

La mise en réseau accélérée doit être activée sur une machine virtuelle Linux. La machine virtuelle doit disposer d’au moins deux interfaces réseau, dont une pour la gestion. Découvrez comment [créer une machine virtuelle Linux en ayant activé la mise en réseau accélérée](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Installer les dépendances de DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL 7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Noyau Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Noyau par défaut**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="setup-virtual-machine-environment-once"></a>Configurer l’environnement de machine virtuelle (une seule fois)

1. [Téléchargez la dernière version de DPDK](https://core.dpdk.org/download). La version 18.02 ou supérieure est nécessaire pour Azure.
2. Commencez par créer la configuration par défaut avec `make config T=x86_64-native-linuxapp-gcc`.
3. Activez Mellanox PMDs dans la configuration générée avec `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Compilez avec `make`.
5. Installez avec `make install DESTDIR=<output folder>`.

# <a name="configure-runtime-environment"></a>Configurer l’environnement d’exécution

Exécutez les commandes suivantes une seule fois, après redémarrage :

1. Hugepages

   * Configurez les Hugepages en exécutant la commande suivante, une seule fois pour tous les nœuds NUMA :

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Créez un répertoire pour un montage avec `mkdir /mnt/huge`.
   *  Montez les Hugepages avec `mount -t hugetlbfs nodev /mnt/huge`.
   *  Vérifiez que les Hugepages sont réservées avec `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Il existe un moyen de modifier le fichier grub de sorte que les Hugepages soient réservées au démarrage en suivant les [instructions](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) relatives à DPDK. Les instructions figurent au bas de la page. Pour une exécution dans une machine virtuelle Linux Azure, modifiez plutôt les fichiers sous /etc/config/grub.d de sorte que les Hugepages soient réservées à tous les redémarrages.

2. Adresses MAC et IP : utilisez `ifconfig –a` pour afficher l’adresse MAC et IP des interfaces réseau. Les interface réseau *VF* et *NETVSC* ont la même adresse MAC, mais seule l’interface réseau *NETVSC* possède une adresse IP. Les interfaces VF s’exécutent en tant qu’interfaces subordonnées des interfaces NETVSC.

3. Adresses PCI

   * Déterminez l’adresse PCI à utiliser pour *VF* avec `ethtool -i <vf interface name>`.
   * Vérifiez que testpmd ne prend pas accidentellement le contrôle du périphérique pci VF pour *eth0* si la mise en réseau accélérée est activée pour *eth0*. Si l’application DPDK a accidentellement pris le contrôle de l’interface réseau de gestion et que cela vous faire perdre la connexion SSH, utilisez la console série pour mettre fin à l’application DPDK ou pour arrêter ou démarrer la machine virtuelle.

4. Chargez *ibuverbs* à chaque redémarrage avec `modprobe -a ib_uverbs`. Pour SLES 15 uniquement, chargez aussi *mlx4_ib* avec `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>PMD fiable (failsafe)

Les applications DPDK doivent s’exécuter via le PMD fiable (sailsafe) qui est exposé dans Azure. Si l’application s’exécute directement via le PMD VF, elle ne recevra pas **tous** les paquets destinés à la machine virtuelle, car certains paquets s’afficheront via l’interface synthétique. Une exécution via le PMD fiable (failsafe) est la garantie que l’application recevra tous les paquets qui lui sont destinés, mais aussi qu’elle continuera de s’exécuter en mode DPDK, même si l’interface VF est révoquée quand l’hôte fait l’objet d’une maintenance. Pour plus d’informations sur le PMD fiable (failsafe), consultez la [bibliothèque de pilotes fiable en mode interrogation](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Exécuter testpmd

Utilisez `sudo` avant que la commande *testpmd* s’exécute en mode root (racine).

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>De base : contrôle d’intégrité, initialisation d’adaptateur fiable (failsafe)

1. Exécutez les commandes suivantes pour démarrer une application testpmd à port unique :

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Exécutez les commandes suivantes pour démarrer une application testpmd à deux ports :

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Si plus de 2 cartes réseau sont utilisées, l’argument `--vdev` suit ce modèle : `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Une fois que l’application a démarré, exécutez `show port info all` pour vérifier les informations de port. Un ou deux ports DPDK assortis de la mention net_failsafe (pas *net_mlx4*) sont alors affichés.
4.  Utilisez `start <port> /stop <port>` pour lancer le trafic.

Les commandes précédentes démarrent *testpmd* en mode interactif, ce qui est recommandé, pour essayer certaines commandes testpmd.

### <a name="basic-single-sendersingle-receiver"></a>De base : expéditeur unique/destinataire unique

Les commandes suivantes impriment régulièrement des statistiques relatives au nombre de paquets par seconde :

1. Du côté de TX, exécutez la commande suivante :

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Du côté de RX, exécutez la commande suivante :

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Si vous exécutez les commandes précédentes sur une machine virtuelle, modifiez *IP_SRC_ADDR* et *IP_DST_ADDR* dans `app/test-pmd/txonly.c` en indiquant l’adresse IP effective des machines virtuelles avant de procéder à la compilation. Sinon, les paquets seront abandonnés avant de parvenir au destinataire.

### <a name="advanced-single-sendersingle-forwarder"></a>Avancé : expéditeur unique/redirecteur unique
Les commandes suivantes impriment régulièrement des statistiques relatives au nombre de paquets par seconde :

1. Du côté de TX, exécutez la commande suivante :

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Du côté de FWD, exécutez la commande suivante :

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Si vous exécutez les commandes précédentes sur une machine virtuelle, modifiez *IP_SRC_ADDR* et *IP_DST_ADDR* dans `app/test-pmd/txonly.c` en indiquant l’adresse IP effective des machines virtuelles avant de procéder à la compilation. Sinon, les paquets seront abandonnés avant de parvenir au redirecteur. Le trafic redirigé ne pourra pas être reçu par une troisième machine, car le redirecteur *testpmd* ne modifie pas les adresses de couche 3, à moins que vous apportiez des modifications au code.

## <a name="references"></a>Références

* [Options EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Commandes Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
