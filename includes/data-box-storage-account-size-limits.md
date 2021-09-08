---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: 1f026d0a8d038e532b9b0ec070a4849ebc8e8530
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803565"
---
Voici les limites de la taille des données qui sont copiées dans un compte de stockage. Assurez-vous que les données que vous chargez sont conformes à ces limites. Pour obtenir les informations les plus récentes sur ces limites, consultez [Objectifs de performance et d’extensibilité du stockage Blob](../articles/storage/blobs/scalability-targets.md) et [Objectifs de performance et d’extensibilité pour Azure Files](../articles/storage/files/storage-files-scale-targets.md). 

| Taille des données copiées dans le compte de stockage Azure                      | Limite par défaut          |
|---------------------------------------------------------------------|------------------------|
| Objet blob de blocs et objet blob de pages                                            | La limite maximale est identique à la [limite de stockage définie pour l’abonnement Azure](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), et elle inclut les données de toutes les sources, y compris Data Box.   |
| Azure Files                                                          | <ul><li>Data Box prend en charge les partages de fichiers volumineux (100 Tio) s’ils sont activés avant la création de la commande Data Box.<ul><li>Si le partage de fichiers volumineux n’est pas activé préalablement à la création de la commande, la taille maximale des partages de fichiers supportée est de 5 Tio.</li><li>Pour améliorer les performances lors des chargements de données, nous vous recommandons d’[activer les partages de fichiers volumineux sur le compte de stockage et d’augmenter la capacité de partage à 100 Tio](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account). Les partages de fichiers volumineux sont uniquement pris en charge pour les comptes de stockage avec un stockage localement redondant (LRS).</li></ul><li>Data Box prend en charge les partages de fichiers Azure Premium qui autorisent un total de 100 Tio pour tous les partages dans le compte de stockage.<ul><li>La capacité maximale utilisable est légèrement inférieure en raison de l’espace utilisé par les journaux de copie et les journaux d’audit. Un minimum de 100 Gio est réservé pour chaque journal de copie et chaque journal d’audit. Pour plus d’informations, consultez [Journal d'audit pour les événements Azure Data Box](../articles/databox/data-box-audit-logs.md).</li><li>Tous les dossiers sous *StorageAccount_AzFiles* doivent respecter cette limite. Pour plus d’informations, consultez [Créer un partage de fichiers Azure](../articles/storage/files/storage-how-to-create-file-share.md).</li></ul></li></ul> |
