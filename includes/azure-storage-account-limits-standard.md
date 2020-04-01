---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334955"
---
Le tableau suivant décrit les limites par défaut pour les comptes de stockage universels Azure v1, v2, stockage d’objets blob, stockage d’objets blob de blocs et Data Lake Storage Gen2. La limite d’*entrée* désigne toutes les données envoyées à un compte de stockage. La limite de *sortie* désigne toutes les données reçues d’un compte de stockage.

| Ressource | Limite |
| --- | --- |
| Nombre de comptes de stockage par région et par abonnement, y compris les comptes Standard, Premium et compatibles Data Lake Storage Gen2.<sup>3</sup> | 250 |
| Capacité maximale du compte de stockage | 5 Pio <sup>1</sup>|
| Nombre maximal de conteneurs d’objets blob, de partages de fichiers, de tables, de files d’attente, d’entités ou de messages par compte de stockage | Aucune limite |
| Taux de demande maximal<sup>1</sup> par compte de stockage | 20 000 demandes par seconde |
| Entrée maximale<sup>1</sup> par compte de stockage (régions des États-Unis, Europe) | 25 Gbits |
| Entrée maximale <sup>1</sup> par compte de stockage (régions autres que les États-Unis et l’Europe) | 5 Gbit/s si RA-GRS/GRS est activé, 10 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v2 et Blob (toutes les régions) | 50 Gbit/s |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS est activé, 30 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions hors États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 15 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de règles de réseau virtuel par compte de stockage | 200 |
| Nombre maximal de règles d’adresse IP par compte de stockage | 200 |

<sup>1</sup> Les comptes de stockage standard Azure prennent en charge la demande de limites supérieures pour la capacité et les entrées. Pour demander une augmentation des limites de compte, contactez le [support Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Si votre compte de stockage dispose d’un accès en lecture activé avec un stockage géoredondant (RA-GRS) ou un stockage géoredondant dans une zone géographique (RA-GZRS), les cibles de sortie de l’emplacement secondaire sont identiques à celles de l’emplacement principal. Voici certaines options de [réplication du Stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) :

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) est un ensemble de fonctionnalités dédiées à l’analytique du Big Data et basées sur le stockage Blob Azure. Les limitations du stockage Azure et du stockage d’objets blob s’appliquent à Data Lake Storage Gen2.

> [!NOTE]
> Microsoft recommande d’utiliser des comptes de stockage universels v2 pour la plupart des scénarios. Vous pouvez facilement mettre à niveau un compte de stockage universel v1 ou un compte de stockage d’objets blob Azure vers un compte universel v2, sans que cela nécessite un temps d’arrêt ou la copie de données. Pour obtenir plus d’informations, consultez [Mettre à niveau vers un compte de stockage v2 à usage général](../articles/storage/common/storage-account-upgrade.md).

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez concevoir votre application afin qu’elle utilise plusieurs comptes de stockage. Ensuite, vous pouvez partitionner vos objets de données sur ces comptes de stockage. Pour plus d’informations sur la tarification en volume, consultez la page [Tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

Tous les comptes de stockage s’exécutent sur une topologie de réseau plat, indépendamment du moment où ils ont été créés. Pour plus d'informations sur l'architecture de réseau plat Stockage Azure et sur son extensibilité, consultez [Stockage Microsoft Azure : un service de stockage cloud hautement disponible à cohérence forte](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
