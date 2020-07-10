---
title: Fichier include
description: Fichier include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/08/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 509568b143c9fbbf236139ca83cb55b0ef39beb0
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145936"
---
Le tableau suivant présente les limites par défaut pour les comptes de stockage d’objets blob, d’objets blob de blocs et V1 et V2 universels. La limite d’*entrée* désigne toutes les données envoyées à un compte de stockage. La limite de *sortie* désigne toutes les données reçues d’un compte de stockage.

| Ressource | Limite |
| --- | --- |
| Nombre de comptes de stockage par région et par abonnement, y compris les comptes de stockage standard et premium.| 250 |
| Capacité maximale du compte de stockage | 5 Pio <sup>1</sup>|
| Nombre maximal de conteneurs d’objets blob, de partages de fichiers, de tables, de files d’attente, d’entités ou de messages par compte de stockage | Aucune limite |
| Taux de demande maximal<sup>1</sup> par compte de stockage | 20 000 demandes par seconde |
| Entrée maximale<sup>1</sup> par compte de stockage (régions des États-Unis, Europe) | 10 Gbits/s |
| Entrée maximale <sup>1</sup> par compte de stockage (régions autres que les États-Unis et l’Europe) | 5 Gbit/s si RA-GRS/GRS est activé, 10 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v2 et Blob (toutes les régions) | 50 Gbit/s |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions des États-Unis) | 20 Gbit/s si RA-GRS/GRS est activé, 30 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de sorties pour les comptes de stockage à usage général v1 (régions hors États-Unis) | 10 Gbit/s si RA-GRS/GRS est activé, 15 Gbit/s pour LRS/ZRS<sup>2</sup> |
| Nombre maximal de règles de réseau virtuel par compte de stockage | 200 |
| Nombre maximal de règles d’adresse IP par compte de stockage | 200 |

<sup>1</sup> Les comptes de stockage standard Azure prennent en charge la demande de limites supérieures pour la capacité et les entrées. Pour demander une augmentation des limites de compte, contactez le [support Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Si votre compte de stockage dispose d’un accès en lecture activé avec un stockage géoredondant (RA-GRS) ou un stockage géoredondant dans une zone géographique (RA-GZRS), les cibles de sortie de l’emplacement secondaire sont identiques à celles de l’emplacement principal. Pour plus d’informations, consultez l’article [Réplication de Stockage Azure](../articles/storage/common/storage-redundancy.md).

> [!NOTE]
> Microsoft recommande d’utiliser des comptes de stockage universels v2 pour la plupart des scénarios. Vous pouvez facilement mettre à niveau un compte de stockage universel v1 ou un compte de stockage d’objets blob Azure vers un compte universel v2, sans que cela nécessite un temps d’arrêt ou la copie de données. Pour obtenir plus d’informations, consultez [Mettre à niveau vers un compte de stockage v2 à usage général](../articles/storage/common/storage-account-upgrade.md).

Si les besoins de votre application dépassent les objectifs d’extensibilité d’un compte de stockage unique, vous pouvez concevoir votre application afin qu’elle utilise plusieurs comptes de stockage. Ensuite, vous pouvez partitionner vos objets de données sur ces comptes de stockage. Pour plus d’informations sur la tarification en volume, consultez la page [Tarification Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

Tous les comptes de stockage s’exécutent sur une topologie de réseau plat, indépendamment du moment où ils ont été créés. Pour plus d'informations sur l'architecture de réseau plat Stockage Azure et sur son extensibilité, consultez [Stockage Microsoft Azure : un service de stockage cloud hautement disponible à cohérence forte](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
