---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75468856"
---
Voici les limites de la taille des données qui sont copiées dans le compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour obtenir les informations les plus récentes sur ces limites, consultez [Objectifs de performance et d’extensibilité du stockage Blob](../articles/storage/blobs/scalability-targets.md) et [Objectifs de performance et d’extensibilité pour Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | 2 Po pour les États-Unis et l’Europe.<br>500 To pour toutes les autres régions, ce qui inclut le Royaume-Uni.  <br> Comprend les données à partir de toutes les sources, notamment Data Box.|
| Azure Files                                                          | 5 Tio par partage.<br> Tous les dossiers sous *StorageAccount_AzureFiles* doivent respecter cette limite.       |
