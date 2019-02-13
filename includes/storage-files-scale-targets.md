---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 72b7bd722bc7634a707e10cbcd45768140ed527a
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55757573"
---
| Ressource | Partages de fichiers Standard | Partages de fichiers Premium (préversion) |
|----------|---------------|------------------------------------------|
| Taille minimale d'un partage de fichiers | (aucun minimum ; paiement à l’utilisation) | 100 Gio |
| Taille maximale d'un partage de fichiers | 5 Tio | 5 Tio |
| Taille maximale d'un fichier dans un partage de fichiers | 1 Tio | 1 Tio |
| Nombre maximal de fichiers dans un partage de fichiers | Aucune limite | Aucune limite |
| Nb max. d’E/S par seconde par partage | 1 000 E/S par seconde | Ligne de base de 5 120 E/S par seconde<br />15 360 E/S avec rafale |
| Nombre maximal de stratégies d’accès stockées par partage de fichiers | 5. | 5. |
| Débit cible pour un partage de fichier unique | Jusqu’à 60 Mio/seconde | Jusqu'à 612 Mio/s (approvisionné) |
| Nombre maximal de descripteurs ouverts par fichier | 2 000 descripteurs ouverts | 2 000 descripteurs ouverts |
| Nombre maximal d’instantanés de partage | 200 instantanés de partage | 200 instantanés de partage |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 2 048 caractères | 2 048 caractères |
| Nombre maximal de caractères des composants de nom de chemin d’accès (dans le chemin d’accès \A\B\C\D, chaque lettre est un composant) | 255 caractères | 255 caractères |