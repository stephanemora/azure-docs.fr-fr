---
title: Ingestion des données dans Azure HPC Cache (préversion) - Script de copie parallèle
description: Comment utiliser un script de copie parallèle pour déplacer des données vers une cible de stockage Blob dans Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: rohogue
ms.openlocfilehash: 4899f946cb358693c969def3fa740af64675d934
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72254516"
---
# <a name="azure-hpc-cache-preview-data-ingest---parallel-copy-script-method"></a>Ingestion des données dans Azure HPC Cache (préversion) - Méthode de script de copie parallèle

Cet article fournit des instructions sur la création d’un script ``parallelcp`` permettant de déplacer des données vers un conteneur de stockage Blob en vue d’une utilisation avec Azure HPC Cache.

Pour plus d’informations sur le déplacement des données vers le stockage Blob pour Azure HPC Cache, consultez [Déplacer les données dans le stockage d’objets blob Azure pour le cache Azure HPC Cache](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Créer le script parallelcp

Le script ci-dessous ajoute le fichier exécutable `parallelcp`. (Ce script est conçu pour Ubuntu ; si vous utilisez une autre distribution, vous devez installer ``parallel`` séparément.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Exemple de copie parallèle

Cet exemple utilise le script de copie parallèle pour compiler ``glibc`` à l’aide de fichiers sources dans Azure HPC Cache.

Les fichiers sources sont stockés sur le point de montage Azure HPC Cache, et les fichiers objets sont stockés sur le disque dur local.

Cet exemple utilise le script de copie parallèle avec les options ``-j`` et ``make`` pour obtenir la parallélisation.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
