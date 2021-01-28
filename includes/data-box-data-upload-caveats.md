---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80e34c117c02c2126b46297c4219effa85e9caa6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792333"
---
- Ne copiez pas les fichiers directement sur les partages précréés. Vous devez créer un dossier dans le partage, puis copier les fichiers dans ce dossier.
- Un dossier sous *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* est un conteneur. Par exemple, les conteneurs sont créés sous la forme suivante *StorageAccount_BlockBlob/container* et *StorageAccount_PageBlob/container*.
- Chaque dossier créé directement sous *StorageAccount_AzureFiles* est traduit en partage de fichiers Azure.
- Si un objet qui est en train d’être copié porte le même nom qu’un objet Azure (objet blob ou fichier), qui est déjà dans le cloud, le service Data Box remplace le fichier dans le cloud.
- Chaque fichier écrit dans les partages *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* est chargé respectivement en tant qu’objet blob de blocs et objet blob de pages.
- Le stockage Blob Azure ne prend pas en charge les répertoires. Si vous créez un dossier sous le dossier *StorageAccount_BlockBlob*, des dossiers virtuels sont alors créés dans le nom de l’objet blob. Pour Azure Files, la structure du répertoire est conservée.
- Une hiérarchie de répertoires vides (sans fichiers) créée sous les dossiers *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* n’est pas chargée.
- S’il se produit des erreurs lors du chargement des données sur Azure, un journal des erreurs est créé dans le compte de stockage cible. Le chemin menant à ce journal des erreurs est disponible à l’issue du chargement ; vous pouvez consulter ce journal afin de procéder aux corrections. Ne supprimez pas les données de la source sans avoir préalablement vérifié les données chargées.
- Les métadonnées de fichier et les autorisations NTFS peuvent être conservées lorsque les données sont chargées vers Azure Files en utilisant les instructions de [conservation des listes de contrôle d’accès, des attributs et des horodateurs de fichiers avec Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).
