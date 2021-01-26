---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98225493"
---
Voici les limites de la taille des données qui sont copiées dans un compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour obtenir les informations les plus récentes sur ces limites, consultez [Objectifs de performance et d’extensibilité du stockage Blob](../articles/storage/blobs/scalability-targets.md) et [Objectifs de performance et d’extensibilité pour Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | La limite maximale est identique à la [limite de stockage définie pour l’abonnement Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), et elle inclut les données de toutes les sources, y compris Data Box.   |
| Azure Files                                                          | Data Box prend en charge les partages de fichiers volumineux (100 Tio) s’ils sont activés avant la création de la commande Data Box. <br> Dans le cas contraire, la taille maximale des partages de fichiers est de 5 Tio. <br> Data Box prend en charge les partages de fichiers Azure Premium qui autorisent un total de 100 Tio pour tous les partages dans le compte de stockage. <br> La capacité maximale utilisable est légèrement inférieure en raison de l’espace utilisé par les journaux de copie et les journaux d’audit. Un minimum de 100 Gio est réservé pour chaque journal de copie et chaque journal d’audit. Pour plus d’informations, consultez [Journal d'audit pour les événements Azure Data Box](../articles/databox/data-box-audit-logs.md). <br> Tous les dossiers sous *StorageAccount_AzureFiles* doivent respecter cette limite. <br> Pour plus d’informations, consultez [Activation et création de partages de fichiers volumineux](../articles/storage/files/storage-files-how-to-create-large-file-share.md).      |
