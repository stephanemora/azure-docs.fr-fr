---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 99314538f90404d7c2b72da0dd2da2d8ac60a08a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59291744"
---
| Ressource | Partages de fichiers Standard | Partages de fichiers Premium (préversion) |
|----------|---------------|------------------------------------------|
| Taille minimale d'un partage de fichiers | Aucun minimum ; paiement | 100 Go ; mise en service |
| Taille maximale d’un partage de fichiers | 5 Tio | 5 To (version préliminaire publique), 100 TIO (version préliminaire publique limitée) |
| Taille maximale d’un fichier dans un partage de fichiers | 1 Tio | 1 Tio |
| Nombre maximal de fichiers dans un partage de fichiers | Aucune limite | Aucune limite |
| Nombre maximal d’IOPS par partage | 1 000 E/S | 5 120 base e/s avec la limite de croissance 15 360 (préversion publique), 100 000 e/s (version préliminaire publique limitée)|
| Nombre maximal de stratégies d’accès stockées par fichier partager | 5. | 5. |
| Débit cible pour un partage de fichier unique | Jusqu’à 60 Mio/s | Consultez premium partage entrant et sortant valeurs du fichier|
| Sortie maximale pour un partage de fichier unique | Consultez le débit de cible de partage de fichiers standard | Jusqu'à 368 Mio/s (préversion publique), jusqu'à 6,204 Mio/s (version préliminaire publique limitée) |
| Entrée maximale pour un partage de fichier unique | Consultez le débit de cible de partage de fichiers standard | Jusqu'à 245 Mio/s (préversion publique), jusqu'à 4 136 Mio/s (version préliminaire publique limitée) |
| Nombre maximal de descripteurs ouverts par fichier | 2 000 descripteurs ouverts | 2 000 descripteurs ouverts |
| Nombre maximal d’instantanés de partage | 200 instantanés de partage | 200 instantanés de partage |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 2 048 caractères | 2 048 caractères |
| Nombre maximal de caractères des composants de nom de chemin d’accès (dans le chemin d’accès \A\B\C\D, chaque lettre est un composant) | 255 caractères | 255 caractères |