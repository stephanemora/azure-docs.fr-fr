---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: ec75cf35bb503e49885664a6682e3be319cc45f6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553641"
---
| Ressource | Partages de fichiers Standard | Partages de fichiers Premium (préversion) |
|----------|---------------|------------------------------------------|
| Taille minimale d'un partage de fichiers | Aucun minimum ; paiement | 100 Gio |
| Taille maximale d’un partage de fichiers | 5 Tio | 5 Tio |
| Taille maximale d’un fichier dans un partage de fichiers | 1 Tio | 1 Tio |
| Nombre maximal de fichiers dans un partage de fichiers | Aucune limite | Aucune limite |
| Nombre maximal d’IOPS par partage | 1 000 E/S | Ligne de base de 5 120 IOPS<br />15 360 E/S avec rafale |
| Nombre maximal de stratégies d’accès stockées par fichier partager | 5. | 5. |
| Débit cible pour un partage de fichier unique | Jusqu’à 60 Mio/s | Jusqu'à 612 Mio/s (approvisionné) |
| Nombre maximal de descripteurs ouverts par fichier | 2 000 descripteurs ouverts | 2 000 descripteurs ouverts |
| Nombre maximal d’instantanés de partage | 200 instantanés de partage | 200 instantanés de partage |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 2 048 caractères | 2 048 caractères |
| Nombre maximal de caractères des composants de nom de chemin d’accès (dans le chemin d’accès \A\B\C\D, chaque lettre est un composant) | 255 caractères | 255 caractères |