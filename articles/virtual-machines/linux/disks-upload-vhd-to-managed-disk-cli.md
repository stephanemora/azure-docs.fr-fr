---
title: Charger un disque dur virtuel sur Azure à l’aide d’Azure CLI
description: Découvrez comment charger un disque dur virtuel sur un disque managé Azure et copier un disque managé d’une région vers une autre à l’aide d’Azure CLI, via un chargement direct.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 51c3933b5ee585c96ad81fe04d379b6771ae81e3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457593"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Charger un disque dur virtuel sur Azure à l’aide d’Azure CLI

Cet article explique comment charger un disque dur virtuel sur un disque managé Azure à partir de votre ordinateur local. Jusqu'à présent, vous deviez suivre un processus complexe qui exigeait la mise en lots de vos données sur un compte de stockage et la gestion de ce compte de stockage. Désormais, pour charger un disque dur virtuel, vous n'avez plus aucun compte de stockage à gérer ni aucune donnée à indexer sur celui-ci. Il vous suffit de créer un disque managé vierge et d'y chargez directement le disque dur virtuel. Cela simplifie le chargement des machines virtuelles locales dans Azure et vous permet de charger un disque dur virtuel d'un maximum de 32 Tio directement sur un disque managé volumineux.

Si vous fournissez une solution de sauvegarde pour les machines virtuelles IaaS dans Azure, nous vous recommandons d'utiliser le chargement direct afin de restaurer les sauvegardes client sur des disques managés. Si vous chargez un disque dur virtuel dans Azure à partir d’une machine externe, les vitesses dépendront de votre bande passante locale. Si vous utilisez une machine virtuelle Azure, votre bande passante sera identique à celle des disques HDD Standard.

Actuellement, le chargement direct est pris en charge pour les disques managés de type HDD Standard, SSD Standard et SSD Premium. Il n'est pas encore pris en charge pour les disques SSD Ultra.

## <a name="prerequisites"></a>Prérequis

- Téléchargez la dernière [version d’AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli).
- Un fichier de disque dur virtuel, stocké localement
- Si vous envisagez de charger un disque dur virtuel à partir d’un emplacement local : Un disque dur virtuel [préparé pour Azure](../windows/prepare-for-upload-vhd-image.md) et stocké localement.
- Ou un disque managé dans Azure, si vous envisagez d’effectuer une action de copie.

## <a name="create-an-empty-managed-disk"></a>Créer un disque managé vierge

Pour charger votre disque dur virtuel dans Azure, vous devez créer un disque managé vierge configuré pour ce processus de chargement. Avant de créer ce disque, vous devez prendre connaissance des informations ci-dessous.

Ce type de disque managé présente deux états uniques :

- ReadToUpload, qui signifie que le disque est prêt à recevoir un chargement, mais qu'aucune [signature d'accès partagé](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) n'a été générée.
- ActiveUpload, qui signifie que le disque est prêt à recevoir un chargement et que la SAS a été générée.

Dans l'un ou l'autre de ces états, le disque managé est facturé au [tarif HDD Standard](https://azure.microsoft.com/pricing/details/managed-disks/), quel que soit le type de disque. Par exemple, un P10 est facturé comme un S10. Ceci s’applique jusqu’à ce que `revoke-access` soit appelé sur le disque managé, ce qui est nécessaire pour joindre le disque à une machine virtuelle.

Avant de pouvoir créer un disque HDD standard vierge pour le chargement, vous devez connaître la taille de fichier (en octets) du disque dur virtuel que vous souhaitez charger. Pour l’obtenir, vous pouvez utiliser `wc -c <yourFileName>.vhd` ou `ls -al <yourFileName>.vhd`. Cette valeur est utilisée lors de la spécification du paramètre **--upload-size-bytes**.

Créez un disque dur standard vierge pour le chargement en spécifiant les paramètres **-–for-upload** et **--upload-size-bytes** dans une applet de commande [disk create](/cli/azure/disk#az-disk-create) :

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Si vous souhaitez charger un disque SSD Premium ou Standard, remplacez **standard_LRS** par **premium_LRS** ou **standardssd_lrs**. Le type SSD Ultra n’est pas encore pris en charge.

Vous venez de créer un disque managé vierge configuré pour le processus de chargement. Pour charger un disque dur virtuel sur le disque, il vous faut une SAS accessible en écriture afin de pouvoir la référencer en tant que destination pour votre chargement.

Pour générer une SAS accessible en écriture de votre disque managé vierge, utilisez la commande suivante :

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Exemple de valeur renvoyée :

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Charger un disque dur virtuel

Maintenant que vous disposez d'une SAS pour votre disque managé vierge, vous pouvez l'utiliser pour définir votre disque managé en tant que destination de votre commande de chargement.

Utilisez AzCopy v10 pour charger votre fichier de disque dur virtuel local sur un disque managé en spécifiant l'URI de la SAS que vous avez générée.

Ce chargement présente le même débit que le disque [HDD Standard](disks-types.md#standard-hdd) correspondant. Par exemple, pour une taille correspondant à S4, vous aurez un débit allant jusqu'à 60 Mio/s. Mais pour une taille correspondant à S70, le débit ira jusqu’à 500 Mio/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Si votre SAS expire pendant le chargement et que vous n’avez pas encore appelé `revoke-access`, vous pouvez obtenir une nouvelle SAS pour poursuivre le chargement en utilisant de nouveau `grant-access`.

Lorsque vous avez terminé le chargement et que vous n’avez plus rien à écrire sur le disque, révoquez la SAS. La révocation de la SAS changera l'état du disque managé et vous permettra de le joindre à une machine virtuelle.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Copier un disque managé

Le chargement direct simplifie également le processus de copie d’un disque managé. Vous pouvez effectuer une copie au sein d’une même région ou entre des régions différentes (vers une autre région).

Le script suivant effectuera cette opération pour vous. Le processus est similaire aux étapes décrites précédemment, à quelques différences près puisque vous utilisez un disque existant.

> [!IMPORTANT]
> Vous devez ajouter un décalage de 512 quand vous fournissez la taille en octets d’un disque managé d’Azure. En effet, Azure omet le pied de page lors du retour de la taille du disque. Si vous ne le faites pas, la copie échouera. Le script suivant s’en charge pour vous.

Remplacez `<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>` et `<yourTargetLocationHere>` (la valeur d’emplacement pourrait par exemple être uswest2) par vos valeurs, puis exécutez le script suivant afin de copier un disque managé.

```bash
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez réussi à charger un disque dur virtuel sur un disque managé, vous pouvez attacher le disque en tant que [disque de données à une machine virtuelle existante](add-disk.md) ou [attacher le disque à une machine virtuelle en tant que disque de système d’exploitation](upload-vhd.md#create-the-vm), pour créer une nouvelle machine virtuelle. 

