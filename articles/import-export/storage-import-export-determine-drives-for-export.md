---
title: Vérifier le nombre de lecteurs nécessaires pour une exportation avec Azure Import/Export | Microsoft Docs
description: Découvrez le nombre de lecteurs dont vous avez besoin pour une exportation à l’aide du service Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: eda9ddd4fa6fca9e4d46c5b86a7f3ab8b7c8cb03
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716481"
---
# <a name="check-number-of-drives-needed-for-an-export-with-azure-importexport"></a>Vérifier le nombre de lecteurs nécessaires pour une exportation avec Azure Import/Export 

Cet article explique comment déterminer le nombre de lecteurs physiques à fournir pour votre commande d’exportation Azure Import/Export. Vous utiliserez l’outil Azure Import/Export. Cet article explique comment installer et utiliser l’outil.

L’outil Azure Import/Export version 1 liste tous les blobs à exporter et calcule leur répartition sur des lecteurs de la taille spécifiée, en prenant en compte les éventuelles surcharges nécessaires, puis évalue le nombre de lecteurs requis pour contenir les blobs et les informations sur l’utilisation des lecteurs.

Vous pouvez utiliser des SSD de 2,5 pouces, des HDD de 2,5 pouces ou des HDD de 3,5 pouces avec votre commande d’exportation. Pour plus d’informations, consultez [Disques pris en charge](storage-import-export-requirements.md#supported-disks).


## <a name="prerequisite"></a>Prérequis

* Vous devez utiliser l’outil sur un système Windows exécutant une [version prise en charge du système d’exploitation](storage-import-export-requirements.md#supported-operating-systems).

## <a name="determine-how-many-drives-you-need"></a>Déterminer le nombre de lecteurs nécessaires

Pour déterminer le nombre de disques physiques dont vous avez besoin pour votre commande d’exportation, procédez comme suit :

1. Téléchargez la version actuelle de l’outil Azure Import/Export version 1 (WAImportExportV1.zip), pour les blobs, sur le système Windows.
  1. [Téléchargez WAImportExport version 1](https://www.microsoft.com/download/details.aspx?id=42659). La version actuelle est 1.5.0.300.
  1. Décompressez le package dans le dossier par défaut : `waimportexportv1`. Par exemple : `C:\WaImportExportV1`.

2. Ouvrez une fenêtre PowerShell ou de ligne de commande avec des privilèges Administrateur. Pour accéder au répertoire du dossier décompressé, exécutez la commande suivante :

   `cd C:\WaImportExportV1`

3. Pour vérifier le nombre de disques nécessaires pour les objets blob sélectionnés, exécutez la commande suivante :

   `WAImportExport.exe PreviewExport /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Les paramètres sont décrits dans le tableau suivant :

    |Paramètre de ligne de commande|Description|
    |--------------------------|-----------------|
    |**/logdir:**|facultatif. Répertoire du journal. Les fichiers journaux détaillés sont écrits dans ce répertoire. Si ce paramètre n’est pas spécifié, le répertoire actif est utilisé en tant que répertoire de journaux.|
    |**/ExportBlobListFile:**|Obligatoire. Chemin d’accès au fichier XML contenant la liste des chemins d’accès ou des préfixes de chemin d’accès aux objets blob à exporter. Format du fichier utilisé dans l’élément `BlobListBlobPath` dans l’opération [Put Job](/rest/api/storageimportexport/jobs) de l’API REST du service Import/Export.|
    |**/DriveSize:**|Obligatoire. Taille des lecteurs à utiliser pour une tâche d’exportation, *par exemple*, 500 Go, 1,5 To.|

    Consultez un [exemple de commande PreviewExport](#example-of-previewexport-command).

4. Vérifiez que vous pouvez accéder en lecture/écriture aux disques qui vont être expédiés pour la tâche d’exportation.

## <a name="example-of-previewexport-command"></a>Exemple de commande PreviewExport

L’exemple suivant illustre la commande `PreviewExport` :

```powershell
    WAImportExport.exe PreviewExport /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

Le fichier de liste d’objets blob à exporter peut contenir des noms et des préfixes d’objets blob, comme l’illustre ce code :

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

### <a name="examples-of-valid-blob-paths"></a>Exemples de chemins d’objets blob valides

Le tableau suivant présente des exemples de chemins de blobs et de préfixes de chemins de blobs valides à utiliser avec les balises &lt;BlobPath&gt; et &lt;BlobPathPrefix&gt; dans votre fichier de liste de blobs.

* Un *chemin d’accès* sélectionne et filtre un seul blob ou fichier.
* Un *préfixe* sélectionne et filtre plusieurs blobs ou plusieurs fichiers.

   | Sélecteur | Chemin.préfixe de blob | Description |
   | --- | --- | --- |
   | Starts With |/ |Exporte tous les objets blob présents dans le compte de stockage. |
   | Starts With |/$root/ |Exporte tous les objets blob présents dans le conteneur racine. |
   | Starts With |/book |Exporte tous les objets blob présents dans un conteneur commençant par le préfixe **book** |
   | Starts With |/music/ |Exporte tous les objets blob présents dans le conteneur **music** |
   | Starts With |/music/love |Exporte tous les objets blob présents dans le conteneur **music** qui commencent par le préfixe **love**. |
   | Equal To |$root/logo.bmp |Exporte l'objet blob **logo.bmp** présent dans le conteneur racine. |
   | Equal To |videos/story.mp4 |Exporte l’objet blob **story.mp4** présent dans le conteneur **videos**. |


### <a name="sample-output"></a>Exemple de sortie

Voici un exemple de sortie, les journaux d’activité d’information étant omis :

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="next-steps"></a>Étapes suivantes

- [Exporter des blobs depuis Stockage Blob Azure](storage-import-export-data-from-blobs.md)
