---
title: Didacticiel - Gestion des disques Azure avec l’interface de ligne de commande Azure
description: Dans ce tutoriel, vous allez apprendre à utiliser Azure CLI afin de créer et gérer des disques Azure pour des machines virtuelles
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 2c55f288631ae77541ad957aeeb26cfc44b29f37
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87483177"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Didacticiel - Gestion des disques Azure avec l’interface de ligne de commande Azure

Les machines virtuelles utilisent des disques pour stocker leur système d’exploitation, leurs applications et leurs données. Lorsque vous créez une machine virtuelle, il est important de choisir une taille de disque et une configuration adaptées à la charge de travail prévue. Ce tutoriel vous montre comment déployer et gérer des disques de machine virtuelle. Vous en apprendrez davantage sur les points suivants :

> [!div class="checklist"]
> * Disques de système d’exploitation et disques temporaires
> * Disques de données
> * Disques Standard et Premium
> * Performances des disques
> * Attachement et préparation des disques de données
> * Captures instantanées de disque


## <a name="default-azure-disks"></a>Disques Azure par défaut

Lorsqu’une machine virtuelle Azure est créée, deux disques sont automatiquement attachés à celle-ci.

**Disque de système d’exploitation** : la taille des disques de système d’exploitation peut atteindre 2 To ; ces disques hébergent le système d’exploitation des machines virtuelles. Le disque de système d’exploitation est nommé */dev/sda* par défaut. La configuration de la mise en cache de disque de système d’exploitation est optimisée pour les performances du système d’exploitation. En raison de cette configuration, le disque de système d’exploitation **ne doit pas** être utilisé pour les applications ou les données. Pour héberger ce type de contenu, utilisez plutôt des disques de données, qui sont décrits plus loin dans ce tutoriel.

**Disque temporaire** : les disques temporaires utilisent un disque SSD qui se trouve sur le même hôte Azure que la machine virtuelle. Les disques temporaires sont extrêmement performants et peuvent être utilisés pour des opérations telles que le traitement de données temporaires. Toutefois, si la machine virtuelle est déplacée vers un nouvel hôte, toutes les données stockées sur un disque temporaire sont supprimées. La taille du disque temporaire est déterminée par la taille de la machine virtuelle. Les disques temporaires sont nommés */dev/sdb* et ont un point de montage */mnt*.

## <a name="azure-data-disks"></a>Disques de données Azure

Des disques de données supplémentaires peuvent être ajoutés pour installer des applications et stocker des données. Les disques de données doivent être utilisés dans les cas où un stockage des données durable et réactif est souhaité. La taille de la machine virtuelle détermine le nombre de disques de données pouvant être attachés à cette machine virtuelle.

## <a name="vm-disk-types"></a>Type de disque de machine virtuelle

Azure propose deux types de disque : Standard et Premium.

### <a name="standard-disk"></a>Disque Standard

Le stockage Standard s’appuie sur des disques durs et offre un stockage économique qui n’en est pas moins performant. Les disques Standard constituent la solution idéale pour une charge de travail de développement et de test économique.

### <a name="premium-disk"></a>Disque Premium

Les disques Premium reposent sur un disque SSD à faible latence et hautes performances. Ils conviennent parfaitement aux machines virtuelles exécutant une charge de travail en production. Le stockage Premium prend en charge les machines virtuelles des séries DS, DSv2, GS et FS. Lorsque vous sélectionnez une taille de disque, la valeur est arrondie au type suivant. Par exemple, si la taille du disque est inférieure à 128 Go, le type de disque est P10. Si la taille du disque est entre 129 Go et 512 Go, le type de disque est P20. Au-dessus de 512 Go, le type de disque est P30.

### <a name="premium-disk-performance"></a>Performances du disque Premium
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Bien que le tableau ci-dessus identifie le nombre max. d’E/S par seconde par disque, un niveau de performances plus élevé est possible en entrelaçant plusieurs disques de données. Par exemple, une machine virtuelle Standard_GS5 peut atteindre un nombre maximum d’E/S par seconde de 80 000. Pour plus d’informations sur le nombre max. d’E/S par seconde par machine virtuelle, consultez [Tailles des machines virtuelles Linux dans Azure](sizes.md).

## <a name="launch-azure-cloud-shell"></a>Lancement d’Azure Cloud Shell

Azure Cloud Shell est un interpréteur de commandes interactif et gratuit que vous pouvez utiliser pour exécuter les étapes de cet article. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte.

Pour ouvrir Cloud Shell, sélectionnez **Essayer** en haut à droite d’un bloc de code. Vous pouvez également lancer Cloud Shell dans un onglet distinct du navigateur en accédant à [https://shell.azure.com/powershell](https://shell.azure.com/bash). Sélectionnez **Copier** pour copier les blocs de code, collez-les dans Cloud Shell, puis appuyez sur Entrée pour les exécuter.

## <a name="create-and-attach-disks"></a>Créer et attacher des disques

Des disques de données peuvent être créés et attachés lors de la création d’une machine virtuelle, mais ils peuvent également être créés et attachés à une machine virtuelle existante.

### <a name="attach-disk-at-vm-creation"></a>Attacher le disque lors de la création d’une machine virtuelle

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az-vm-create). L’exemple suivant crée une machine virtuelle nommée *myVM*, ajoute un compte d’utilisateur appelé *azureuser* et génère des clés SSH si elles n’existent pas. L’argument `--datadisk-sizes-gb` est utilisé pour spécifier qu’un disque supplémentaire doit être créé et attaché à la machine virtuelle. Pour créer et attacher plusieurs disques, utilisez une liste séparée par des espaces des valeurs de taille de disque. Dans l’exemple suivant, une machine virtuelle est créée avec deux disques de données, tous deux de 128 Go. La taille des disques étant de 128 Go, ces disques sont configurés en tant que disques P10, qui fournissent 500 E/S par seconde maximum par disque.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Attacher un disque à une machine virtuelle existante

Pour créer et attacher un nouveau disque à une machine virtuelle existante, utilisez la commande [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). L’exemple suivant crée un disque Premium de 128 Go et l’attache à la machine virtuelle créée à l’étape précédente.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Préparer les disques de données

Une fois qu’un disque a été attaché à la machine virtuelle, le système d’exploitation doit être configuré pour utiliser le disque. L’exemple suivant montre comment configurer manuellement un disque. Ce processus peut également être automatisé à l’aide de cloud-init, qui est présenté dans un [prochain didacticiel](./tutorial-automate-vm-deployment.md).


Créez une connexion SSH avec la machine virtuelle. Remplacez l’exemple d’adresse IP par l’adresse IP publique de la machine virtuelle.

```console
ssh 10.101.10.10
```

Partitionnez le disque avec `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Écrivez un système de fichiers dans la partition avec la commande `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Montez le nouveau disque afin qu’il soit accessible dans le système d’exploitation.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Le disque est désormais accessible via le point de montage *datadrive*, qui peut être vérifié en exécutant la commande `df -h`.

```bash
df -h
```

La sortie indique le nouveau lecteur monté sur */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Pour vous assurer que le lecteur est remonté automatiquement après un redémarrage, vous devez l’ajouter au fichier */etc/fstab*. Pour ce faire, obtenez l’UUID du disque avec l’utilitaire `blkid`.

```bash
sudo -i blkid
```

La sortie affiche l’UUID du lecteur, `/dev/sdc1` dans cet exemple.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Ajoutez une ligne semblable à ce qui suit au fichier */etc/fstab*.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Maintenant que le disque a été configuré, fermez la session SSH.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Prendre un instantané d’un disque

Lors d’une capture instantanée du disque, Azure crée une copie en lecture seule à un moment donné du disque. Les captures instantanées de machine virtuelle Azure sont utiles pour enregistrer rapidement l’état d’une machine virtuelle avant d’apporter des modifications à la configuration. En cas de problème ou d’erreur, la machine virtuelle peut être restaurée à l’aide d’un instantané. Lorsqu’une machine virtuelle a plusieurs disques, une capture instantanée de chaque disque est prise. Pour les sauvegardes cohérentes des applications, pensez à arrêter la machine virtuelle avant de prendre des captures instantanées du disque. Vous pouvez également utiliser le [service Sauvegarde Azure](../../backup/index.yml), qui vous permet d’effectuer des sauvegardes automatisées alors que la machine virtuelle est en cours d’exécution.

### <a name="create-snapshot"></a>Créer l’instantané

Avant de créer une capture instantanée de disque de machine virtuelle, l’ID ou le nom du disque est nécessaire. Utilisez la commande [az vm show](/cli/azure/vm#az-vm-show) pour renvoyer l’ID du disque. Dans cet exemple, l’ID du disque est stocké dans une variable pour qu’il puisse être utilisé dans une étape ultérieure.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Maintenant que vous avez l’ID du disque de machine virtuelle, la commande suivante crée une capture instantanée du disque.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Création d’un disque à partir d’une capture instantanée

Cet instantané peut ensuite être converti en disque qui peut être utilisé pour recréer la machine virtuelle.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restauration de la machine virtuelle à partir de l’instantané

Pour illustrer la récupération de machine virtuelle, supprimez la machine virtuelle existante.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Créez une nouvelle machine virtuelle à l’aide du disque d’instantané.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Rattacher un disque de données

Tous les disques de données doivent être rattachés à la machine virtuelle.

Trouvez d’abord le nom du disque de données à l’aide de la commande [az disk list](/cli/azure/disk#az-disk-list). Cet exemple place le nom du disque dans une variable nommée *datadisk*, qui est utilisée à l’étape suivante.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Utilisez la commande [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) pour attacher le disque.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances concernant les disques de machine virtuelle, notamment concernant les points suivants :

> [!div class="checklist"]
> * Disques de système d’exploitation et disques temporaires
> * Disques de données
> * Disques Standard et Premium
> * Performances des disques
> * Attachement et préparation des disques de données
> * Captures instantanées de disque

Passez au didacticiel suivant pour en apprendre davantage sur l’automatisation de la configuration de machine virtuelle.

> [!div class="nextstepaction"]
> [Automatiser la configuration de machine virtuelle](./tutorial-automate-vm-deployment.md)
