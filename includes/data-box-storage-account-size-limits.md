---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 5c5732a825116d4762c3a27bcbf9eac195327afb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85378536"
---
Voici les limites de la taille des données qui sont copiées dans le compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour obtenir les informations les plus récentes sur ces limites, consultez [Objectifs de performance et d’extensibilité du stockage Blob](../articles/storage/blobs/scalability-targets.md) et [Objectifs de performance et d’extensibilité pour Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | La limite maximale est identique à la [limite de stockage définie pour l’abonnement Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits), et elle inclut les données de toutes les sources, y compris Data Box.|
| Azure Files                                                          | La taille maximale des partages de fichiers Standard est de 5 To. <br> La taille maximale des partages de fichiers Premium est de 100 Tio par partage.<br> Tous les dossiers sous *StorageAccount_AzureFiles* doivent respecter cette limite.       |
