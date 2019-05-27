---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114814"
---
| Ressource | Cible        |
|----------|---------------|
| Taille maximale du conteneur d’objets blob unique | Identique à la capacité du compte de stockage maximale |
| Nombre maximal de blocs dans un bloc d’objets blob ou ajouter des objets blob | 50 000 blocs |
| Taille maximale d’un bloc dans un objet blob de blocs | 100 Mio |
| Taille maximale d’un objet blob de blocs | 50 000 x 100 Mio (environ 4,75 TIO) |
| Taille maximale d’un bloc dans un objet blob d’ajout | 4 Mio |
| Taille maximale d’un objet blob d’ajout | 50 000 x 4 Mio (environ 195 Gio) |
| Taille maximale d’un objet blob de pages | 8 Tio |
| Nombre maximal de stratégies d’accès stockées par conteneur d’objets blob | 5. |
|Débit cible pour un objet blob unique |Jusqu'à des limites d’entrées/sorties de compte de stockage<sup>1</sup> |

<sup>1</sup> débit d’objet unique dépend de plusieurs facteurs, y compris, mais sans limitation : d’accès concurrentiel, taille de la demande, niveau de performance, la vitesse de la source pour les téléchargements et de destination pour les téléchargements. Pour tirer parti des [objet blob de blocs de haut débit](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) améliorations des performances, utilisez une taille de demande Put Blob ou Put Block > 4 MiB (> 256 Kio pour stockage blob par blocs de performances premium ou pour le stockage Data Lake Gen2).
