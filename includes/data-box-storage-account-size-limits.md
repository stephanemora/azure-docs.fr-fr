---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 0975df8ee9da4a239dbd72e468b967c88ab3feed
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277230"
---
Voici les limites de la taille des données qui sont copiées dans le compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour consulter les dernières informations relatives à ces limites, accédez à [Objectifs de mise à l’échelle du stockage Blob Azure](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) et [Objectifs de mise à l’échelle Azure Files](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | 2 Po pour les États-Unis et l’Europe.<br>500 To pour toutes les autres régions, ce qui inclut le Royaume-Uni.  <br> Comprend les données à partir de toutes les sources, notamment Data Box.|
| Azure Files                                                          | 5 Tio par partage.<br> Tous les dossiers sous *StorageAccount_AzureFiles* doivent respecter cette limite.       |
