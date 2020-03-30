---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392415"
---
| Ressource | Cible        |
|----------|---------------|
| Taille maximale du conteneur d’objets blob unique | Identique à la capacité maximale du compte de stockage |
| Nombre maximal de blocs dans un objet blob de blocs ou ajouter des objets blob | 50 000 blocs |
| Taille maximale d’un bloc dans un objet blob de blocs | 100 Mio |
| Taille maximale d’un objet blob de blocs | 50 000 x 100 Mio (environ 4,75 Tio) |
| Taille maximale d’un bloc dans un objet blob d’ajout | 4 Mio |
| Taille maximale d’un objet blob d’ajout | 50 000 x 4 Mio (environ 195 Gio) |
| Taille maximale d’un objet blob de pages | 8 Tio |
| Nombre maximal de stratégies d’accès stockées par conteneur d’objets blob | 5 |
|Taux de requêtes cibles pour un seul objet blob | Jusqu’à 500 requêtes par seconde |
|Débit cible pour un blob de page unique | Jusqu’à 60 Mio par seconde |
|Débit cible pour un objet blob de blocs unique |Jusqu’à la limite d’entrée/sortie du compte de stockage<sup>1</sup> |

<sup>1</sup> Le débit pour un seul objet blob dépend de plusieurs facteurs, dont et sans s’y limiter : concurrence, taille de la demande, niveau de performance, vitesse des chargements à la source et des téléchargements à la destination. Pour tirer parti des améliorations des performances des [objets blob de blocs à haut débit](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/), chargez des objets blob ou des blocs plus volumineux. En particulier, appelez l’opération [Put Blob](/rest/api/storageservices/put-blob) ou [Put Block](/rest/api/storageservices/put-block) avec une taille d’objet blob ou de bloc supérieure à 4 Mio pour les comptes de stockage standard. Pour les objets blob de blocs Premium ou pour les comptes de stockage Data Lake Storage Gen2, utilisez une taille de bloc ou d’objet blob supérieure à 256 Kio.
