---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200298"
---
Voici une liste des comptes de stockage et des types de stockage pris en charge pour l’appareil Data Box. Pour obtenir la liste complète des différents types de comptes de stockage et de leurs fonctionnalités, consultez [Types de comptes de stockage](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

Pour les commandes d’importation, le tableau suivant indique les comptes de stockage pris en charge.

| **Compte de stockage / Type de stockage pris en charge** | **Objet blob de blocs** |**Objet blob de pages*** |**Azure Files** |**Remarques**|
| --- | --- | -- | -- | -- |
| Standard classique | O | O | O |
| Édition Standard v1 à usage général  | O | O | O | Les objets chauds et froids sont pris en charge.|
| Comptes de stockage à usage général v1 Premium  |  | O| | |
| Comptes de stockage à usage général v2 Standard  | O | O | O | Les objets chauds et froids sont pris en charge.|
| Comptes de stockage à usage général v2 Premium  |  |O | | |
| Stockage d'objets blob Standard |O | | |Les objets chauds et froids sont pris en charge. |

\* *Les données chargées dans des objets blob de pages doivent être alignées sur 512 octets (disques durs virtuels, par exemple).*

Pour les commandes d’exportation, le tableau suivant indique les comptes de stockage pris en charge.

| **Compte de stockage / Type de stockage pris en charge** | **Objet blob de blocs** |**Objet blob de pages*** |**Azure Files** |**Niveaux d’accès pris en charge**|
| --- | --- | -- | -- | -- |
| Standard classique | O | O | O | |
| Édition Standard v1 à usage général  | O | O | O | Chaud, froid|
| Comptes de stockage à usage général v1 Premium  |  | O| | |
| Comptes de stockage à usage général v2 Standard  | O | O | O | Chaud, froid|
| Comptes de stockage à usage général v2 Premium  |  |O | | |
| Stockage d'objets blob Standard |O | | |Chaud, froid |
| Stockage d’objets blob de blocs Premium |O | | |Chaud, froid |
| Stockage d’objets blob de pages Premium | |O | | |

> [!IMPORTANT]
> - Pour les comptes universels, Data Box ne prend pas en charge les types de stockage File d’attente, Table, Disque et Azure Data Lake Gen2.
> - Data Box ne prend pas en charge l’ajout (append) d’objets blob pour les comptes de Stockage Blob et de stockage d’objets blob de blocs.
> - Data Box ne prend pas en charge les comptes de Stockage Fichier Premium.
> - Les données chargées dans des objets blob de pages doivent être alignées sur 512 octets (p. ex., disques durs virtuels).
> - Le volume maximal de données exportées est de 80 To.
> - L’historique des fichiers et les instantanés d’objets blob ne sont pas exportés.


