---
title: Configurer l’interface de transmission de messages pour HPC - Machines virtuelles Azure | Microsoft Docs
description: Découvrez comment configurer MPI pour HPC sur Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023988"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurer l’interface de transmission de messages pour HPC

Les charges de travail de l’interface de transmission de messages (MPI) sont une partie essentielle des charges de travail HPC traditionnelles. Les tailles de machines virtuelles compatibles SR-IOV sur Azure permettent d’utiliser presque toutes les versions de MPI. 

L’exécution de travaux MPI sur des machines virtuelles nécessite la configuration de clés de partition (p-keys) sur un locataire. Suivez les étapes de la section [Découvrir les clés de partition](#discover-partition-keys) pour en savoir plus sur la détermination des valeurs des p-keys.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) offre des performances optimales sur IB et fonctionne avec MPICH et OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Installez UCX comme indiqué précédemment.

```bash
sudo yum install –y openmpi
```

Compilez OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Exécutez OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Vérifiez votre clé de partition comme indiqué ci-dessus.

## <a name="mpich"></a>MPICH

Installez UCX comme indiqué précédemment.

Créez MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Exécution de MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Vérifiez votre clé de partition comme indiqué ci-dessus.

## <a name="mvapich2"></a>MVAPICH2

Créer MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Exécution de MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community Edition

Installez les packages requis pour Platform MPI.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Suivez le processus d’installation.

## <a name="intel-mpi"></a>Intel MPI

[Téléchargez Intel MPI](https://software.intel.com/mpi-library/choose-download).

Modifiez la variable d’environnement I_MPI_FABRICS selon la version. Utilisez `I_MPI_FABRICS=shm:ofa` pour Intel MPI 2018 et `I_MPI_FABRICS=shm:ofi` pour 2019.

L’épinglage de processus fonctionne correctement pour 15, 30 et 60 PPN par défaut.

## <a name="osu-mpi-benchmarks"></a>OSU MPI Benchmarks

[Téléchargez OSU MPI Benchmarks](http://mvapich.cse.ohio-state.edu/benchmarks/) et décompressez le fichier.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Créez des bancs d’essai à l’aide d’une bibliothèque MPI particulière :

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI Benchmarks se trouve dans le dossier `mpi/`.


## <a name="discover-partition-keys"></a>Découvrir les clés de partition

Découvrez les clés de partition (p-keys) pour communiquer avec d’autres machines virtuelles au sein du même locataire (groupe à haute disponibilité ou groupe identique de machines virtuelles).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

La plus grande des deux est la clé de locataire, qui doit être utilisée avec MPI. Exemple : Si les éléments suivants sont les p-keys, 0x800b doit être utilisé avec MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Utiliser une partition autre que de la clé de partition par défaut (0x7fff). UCX requiert l’effacement de l’octet le plus significatif de la p-key. Par exemple, définissez UCX_IB_PKEY comme étant 0x000b pour 0x800b.

Notez également que tant que le locataire (AVSet ou VMSS) existe, les PKEYs restent les mêmes. Cela est vrai même lorsque les nœuds sont ajoutés ou supprimés. Les nouveaux locataires obtiennent des PKEYs différents.


## <a name="set-up-user-limits-for-mpi"></a>Configurez un nombre limite d’utilisateurs pour MPI

Configurez un nombre limite d’utilisateurs pour MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Configurez les clés SSH pour MPI

Configurer les clés SSH pour les types de MPI qui en ont besoin.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

La syntaxe ci-dessus suppose un répertoire de base partagé, sinon le répertoire .ssh doit être copié dans chaque nœud.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) sur Azure.
