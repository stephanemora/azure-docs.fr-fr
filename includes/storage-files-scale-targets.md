---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: d76338ccd4dcdf7a9a9a96baa1fb47ed5272d5ff
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821978"
---
| Ressource | Partages de fichiers Standard | Partages de fichiers Premium |
|----------|---------------|------------------------------------------|
| Taille minimale d'un partage de fichiers | (aucun minimum ; paiement à l’utilisation) | 100 GiB provisionné |
| Taille maximale d’un partage de fichiers | 100 Gio*, 5 Tio | 100 Tio |
| Taille maximale d’un fichier dans un partage de fichiers | 1 Tio | 1 Tio |
| Nombre maximal de fichiers dans un partage de fichiers | Aucune limite | Aucune limite |
| Maximum d’E/S par seconde par partage | 10 000 IOPS*, 1000 IOPS | 100 000 E/S par seconde |
| Nombre maximal de stratégies d’accès stockées par partage de fichiers | 5\. | 5\. |
| Débit cible pour un partage de fichier unique | jusqu’à 300 MiB/s*, jusqu’à 60 MiB/s  | Voir les valeurs d’entrée et de sortie du partage de fichiers Premium|
| Sortie maximale pour un partage de fichier unique | Voir le débit de cible du partage de fichiers standard | Jusqu’à 6 204 Mio/s |
| Entrée maximale pour un partage de fichier unique | Voir le débit de cible du partage de fichiers standard | Jusqu’à 4 136 Mio/s |
| Nombre maximal de descripteurs ouverts par fichier | 2 000 descripteurs ouverts | 2 000 descripteurs ouverts |
| Nombre maximal d’instantanés de partage | 200 instantanés de partage | 200 instantanés de partage |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 2 048 caractères | 2 048 caractères |
| Nombre maximal de caractères des composants de nom de chemin d’accès (dans le chemin d’accès \A\B\C\D, chaque lettre est un composant) | 255 caractères | 255 caractères |

\* Non disponible dans toutes les régions ; consultez [Régions disponibles](../articles/storage/files/storage-files-planning.md#regional-availability) pour obtenir la liste des régions disponibles.
