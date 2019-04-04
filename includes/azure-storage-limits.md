---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: dccc967e7899d8cc9c4bde6829d6fc44f9cba4e0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58890940"
---
Le tableau suivant décrit les limites par défaut du Stockage Azure. Le *entrée* limite s’applique à toutes les données à partir des demandes qui sont envoyées à un compte de stockage. Le *sortie* limite s’applique à toutes les données à partir des réponses sont reçues d’un compte de stockage.

| Ressource | Limite par défaut |
| --- | --- |
| Nombre de comptes de stockage par région et par abonnement, y compris les comptes standard et premium | 250 |
| Capacité de compte de stockage maximale | 2 Go pour les États-Unis et Europe, 500 To pour toutes les autres régions, ce qui inclut le Royaume-Uni |
| Nombre maximal de conteneurs d’objets blob, objets BLOB, partages de fichiers, tables, files d’attente, entités ou des messages par compte de stockage | Aucune limite |
| Taux de demande maximal<sup>1</sup> par compte de stockage | 20 000 demandes par seconde |
| Entrée maximale<sup>1</sup> par compte de stockage (régions des États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 20 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Entrée maximale<sup>1</sup> par compte de stockage (régions hors États-Unis) | 5 Gbit/s si RA-GRS/GRS est activé, 10 Gbits/s pour LRS/ZRS<sup>2</sup> |
| Sortie maximale pour un usage général v2 et comptes de stockage d’objets Blob (toutes les régions) | 50 Gbit/s |
| Sortie maximale pour les comptes de stockage à usage général v1 (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS est activé, 30 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Sortie maximale pour les comptes de stockage à usage général v1 (régions hors États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 15 Gbit/s pour LRS/ZRS<sup>2</sup> |

<sup>1</sup>comptes de stockage Standard azure prend en charge des limites plus élevées pour l’entrée par demande. Pour demander une augmentation des limites de compte pour la sortie, contactez le [support Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> [Les options de réplication du Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) sont notamment :
* **RA-GRS** : Stockage géoredondant avec accès en lecture. Si RA-GRS est activé, les cibles de sortie pour l’emplacement secondaire sont identiques à celles de l’emplacement principal.
* **GRS** : Stockage géoredondant. 
* **ZRS** : Stockage redondant interzone.
* **LRS** : Stockage localement redondant. 

> [!NOTE]
> Nous vous recommandons d’utiliser un compte de stockage à usage général v2 pour la plupart des scénarios. Vous pouvez facilement mettre à niveau un v1 à usage général ou un compte de stockage Blob Azure vers un compte v2 à usage général sans temps d’arrêt et sans avoir besoin de copier des données.
>
> Pour plus d’informations sur les comptes de stockage Azure, consultez [vue d’ensemble du compte de stockage](../articles/storage/common/storage-account-overview.md). 

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez concevoir votre application afin qu’elle utilise plusieurs comptes de stockage. Ensuite, vous pouvez partitionner vos objets de données sur ces comptes de stockage. Pour plus d’informations sur la tarification de volume, consultez [tarification stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

Tous les comptes de stockage s’exécutent sur la topologie de réseau plat et prennent en charge les objectifs d'extensibilité et de performances décrits dans cet article, quel que soit le moment où ils ont été créés. Pour plus d'informations sur l'architecture de réseau plat Stockage Azure et sur son extensibilité, consultez [Stockage Microsoft Azure : un service de stockage cloud hautement disponible à cohérence forte](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

