---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: tamram
ms.openlocfilehash: 213ecee34df46c0a408e7034a07ab864c96b340b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114515"
---
| Ressource | Partages de fichiers Standard | Partages de fichiers Premium (préversion) |
|----------|---------------|------------------------------------------|
| Taille minimale d'un partage de fichiers | Aucun minimum ; paiement | 100 Go ; mise en service |
| Taille maximale d’un partage de fichiers | 5 Tio | 100 TiB |
| Taille maximale d’un fichier dans un partage de fichiers | 1 Tio | 1 Tio |
| Nombre maximal de fichiers dans un partage de fichiers | Illimité | Illimité |
| Nombre maximal d’IOPS par partage | 1 000 E/S | 100 000 E/S par seconde |
| Nombre maximal de stratégies d’accès stockées par fichier partager | 5. | 5. |
| Débit cible pour un partage de fichier unique | Jusqu’à 60 Mio/s | Consultez premium partage entrant et sortant valeurs du fichier|
| Sortie maximale pour un partage de fichier unique | Consultez le débit de cible de partage de fichiers standard | Jusqu'à 6,204 Mio/s |
| Entrée maximale pour un partage de fichier unique | Consultez le débit de cible de partage de fichiers standard | Jusqu'à 4 136 Mio/s |
| Nombre maximal de descripteurs ouverts par fichier | 2 000 descripteurs ouverts | 2 000 descripteurs ouverts |
| Nombre maximal d’instantanés de partage | 200 instantanés de partage | 200 instantanés de partage |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 2 048 caractères | 2 048 caractères |
| Nombre maximal de caractères des composants de nom de chemin d’accès (dans le chemin d’accès \A\B\C\D, chaque lettre est un composant) | 255 caractères | 255 caractères |