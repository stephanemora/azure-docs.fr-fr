---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736975"
---
Voici les limites de la taille des données qui sont copiées dans le compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour obtenir les informations les plus récentes sur ces limites, consultez [Objectifs de performance et d’extensibilité du stockage Blob](../articles/storage/blobs/scalability-targets.md) et [Objectifs de performance et d’extensibilité pour Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | 2 Po pour les États-Unis et l’Europe.<br>500 To pour toutes les autres régions, ce qui inclut le Royaume-Uni.  <br> Comprend les données à partir de toutes les sources, notamment Data Box.|
| Azure Files                                                          | Taille maximale des partages de fichiers standard 100TiB*, 5 To, partages de fichiers Premium de 100 Tio par partage.<br> Tous les dossiers sous *StorageAccount_AzureFiles* doivent respecter cette limite.       |
