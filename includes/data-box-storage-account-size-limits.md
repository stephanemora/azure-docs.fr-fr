---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 7b46360886387bc4ab48f764edb5d01dc52dc812
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244647"
---
Voici les limites de la taille des données qui sont copiées dans le compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour consulter les dernières informations relatives à ces limites, accédez à [Objectifs de mise à l’échelle du stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) et [Objectifs de mise à l’échelle Azure Files](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | 500 Tio par compte de stockage. <br> Comprend les données à partir de toutes les sources, notamment Data Box.|
| Fichier Azure                                                          | 5 Tio par partage.<br> Tous les dossiers sous *StorageAccount_AzureFiles* doivent respecter cette limite.       |
