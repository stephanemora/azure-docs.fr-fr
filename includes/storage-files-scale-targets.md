---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9f259c3e403e933c847ac56000b1db2cd594caf5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449932"
---
| Ressource | Partages de fichiers Standard | Partages de fichiers Premium |
|----------|---------------|------------------------------------------|
| Taille minimale d'un partage de fichiers | (aucun minimum ; paiement à l’utilisation) | 100 GiB provisionné |
| Taille maximale d’un partage de fichiers | 5 To (GA), 100 Tio (préversion) | 100 Tio |
| Taille maximale d’un fichier dans un partage de fichiers | 1 Tio | 1 Tio |
| Nombre maximal de fichiers dans un partage de fichiers | Aucune limite | Aucune limite |
| Maximum d’E/S par seconde par partage | 1 000 IOPS (GA), 10 000 IOPS (préversion) | 100 000 E/S par seconde |
| Nombre maximal de stratégies d’accès stockées par partage de fichiers | 5\. | 5\. |
| Débit cible pour un partage de fichier unique | Jusqu’à 60 Mio/s (GA), jusqu’à 300 Mio/s (préversion) | Voir les valeurs d’entrée et de sortie du partage de fichiers Premium|
| Sortie maximale pour un partage de fichier unique | Voir le débit de cible du partage de fichiers standard | Jusqu’à 6 204 Mio/s |
| Entrée maximale pour un partage de fichier unique | Voir le débit de cible du partage de fichiers standard | Jusqu’à 4 136 Mio/s |
| Nombre maximal de descripteurs ouverts par fichier | 2 000 descripteurs ouverts | 2 000 descripteurs ouverts |
| Nombre maximal d’instantanés de partage | 200 instantanés de partage | 200 instantanés de partage |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 2 048 caractères | 2 048 caractères |
| Nombre maximal de caractères des composants de nom de chemin d’accès (dans le chemin d’accès \A\B\C\D, chaque lettre est un composant) | 255 caractères | 255 caractères |