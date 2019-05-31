---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: b159ec8620fa8c93e4917f73be9b9898e1b4fbcc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244587"
---
- Ne copiez pas les fichiers directement à tous les partages précréés. Vous devez créer un dossier sous le partage et puis copiez les fichiers dans ce dossier.
- Un dossier sous *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* est un conteneur. Par exemple, les conteneurs sont créés sous la forme suivante *StorageAccount_BlockBlob/container* et *StorageAccount_PageBlob/container*.
- Chaque dossier créé directement sous *StorageAccount_AzureFiles* est traduit en partage de fichiers Azure.
- Si un objet Azure existant (tel qu’un objet blob ou un fichier) présent dans le cloud a le même nom que l’objet qui est en train d’être copié, le service Data Box remplace le fichier dans le cloud.
- Chaque fichier écrit dans les partages *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* est chargé respectivement en tant qu’objet blob de blocs et objet blob de pages.
- Stockage d’objets blob Azure ne prend pas en charge les répertoires. Si vous créez un dossier sous le dossier *StorageAccount_BlockBlob*, des dossiers virtuels sont alors créés dans le nom de l’objet blob. Pour Azure Files, la structure du répertoire est conservée.
- Les vides d’une hiérarchie de répertoires (sans les fichiers) créée sous *StorageAccount_BlockBlob* et *StorageAccount_PageBlob* dossiers n’est pas chargé.
- S’il se produit des erreurs lors du chargement des données sur Azure, un journal des erreurs est créé dans le compte de stockage cible. Le chemin menant à ce journal des erreurs est disponible à l’issue du chargement ; vous pouvez consulter ce journal afin de procéder aux corrections. Ne supprimez les données à partir de la source sans vérifier les données téléchargées.
