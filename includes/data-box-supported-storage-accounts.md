---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 10/07/2021
ms.author: alkohli
ms.openlocfilehash: 8c823b6709dbd5010ec48c2fbca185d9a211cb40
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000066"
---
Voici une liste des comptes de stockage et des types de stockage pris en charge pour un appareil Data Box. Pour obtenir la liste complète de toutes les capacités pour tous les types de comptes de stockage, consultez [Types de comptes de stockage](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

#### <a name="supported-storage-accounts-for-imports"></a>Comptes de stockage pris en charge pour les importations

Pour les commandes d’importation, le tableau suivant indique les comptes de stockage pris en charge.

| **Compte de stockage / Type de stockage pris en charge** | **Objet blob de blocs** |**Objet blob de pages**<sup>1</sup> |**Azure Files** |**Niveaux d’accès pris en charge**|
| --- | --- | -- | -- | -- |
| Standard classique | O | O | O |
| Édition Standard v1 à usage général  | O | O | O | Chaud, froid |
| Comptes de stockage à usage général v1 Premium  |  | O| | |
| Standard v2 universel<sup>2</sup>  | O | O | O | Chaud, froid|
| Comptes de stockage à usage général v2 Premium  |  |O | |  |
| FileStorage Azure Premium |  |  | O |  |  
| Stockage d'objets blob Standard | O | | | Chaud, froid |


<sup>1</sup> *Les données chargées dans des objets blob de pages doivent être alignées sur 512 octets (p. ex., disques durs virtuels).*

<sup>2</sup> *Azure Data Lake Storage Gen2 (ADLS Gen2) est pris en charge pour les importations, mais pas pour les exportations.*


#### <a name="supported-storage-accounts-for-exports"></a>Comptes de stockage pris en charge pour les exportations

Pour les commandes d’exportation, le tableau suivant indique les comptes de stockage pris en charge.

| **Compte de stockage / Type de stockage pris en charge** | **Objet blob de blocs** |**Objet blob de pages** _ |_ *Fichiers Azure** |**Niveaux d’accès pris en charge**|
| --- | --- | -- | -- | -- |
| Standard classique | O | O | O | |
| Édition Standard v1 à usage général  | O | O | O | Chaud, froid |
| Comptes de stockage à usage général v1 Premium  |  | O| | |
| Comptes de stockage à usage général v2 Standard  | O | O | O | Chaud, froid |
| Comptes de stockage à usage général v2 Premium  |  |O | | |
| FileStorage Azure Premium |  |  | O |  |
| Stockage d'objets blob Standard |O | | | Chaud, froid |
| Stockage d’objets blob de blocs Premium |O | | | Chaud, froid |
| Stockage d’objets blob de pages Premium | |O | | |

#### <a name="caveats-for-storage-accounts"></a>Avertissements pour les comptes de stockage

- Pour les comptes à usage général :
  - Pour les ordres d’importation, Data Box ne prend pas en charge les types de stockage File d’attente, Table et Disque.
  - Pour les commandes d’exportation, Data Box ne prend pas en charge les types de stockage File d’attente, Table, Disque et Azure Data Lake Gen2.
- Data Box ne prend pas en charge l’ajout (append) d’objets blob pour les comptes de Stockage Blob et de stockage d’objets blob de blocs.
- Le protocole NFS (Network File System) 3.0 dans le stockage d’objets BLOB Azure n’est pas pris en charge avec Data Box.
- Les données chargées dans des objets blob de pages doivent être alignées sur 512 octets (p. ex., disques durs virtuels).
- Pour les exportations :
  - Le volume maximal de données exportées est de 80 To.
  - L’historique des fichiers et les instantanés d’objets blob ne sont pas exportés.
  - Les objets BLOB d’archive ne sont pas pris en charge pour l’exportation. Réalimentez les objets BLOB au niveau Archive avant d’exporter. Pour plus d’informations, consultez [réalimentation d’un objet BLOB archivé sur un niveau en ligne](../articles/storage/blobs/archive-rehydrate-overview.md).