---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8034a7a520cc50b2e57e0ad26205da2e408296a1
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2020
ms.locfileid: "88760568"
---
| Ressource | Partages de fichiers Standard | Partages de fichiers Premium |
|----------|---------------|------------------------------------------|
| Taille minimale d'un partage de fichiers | (aucun minimum ; paiement à l’utilisation) | 100 GiB provisionné |
| Taille maximale d’un partage de fichiers | 100 Gio*, 5 Tio | 100 Tio |
| Taille maximale d’un fichier dans un partage de fichiers | 1 Tio | 4 Tio |
| Nombre maximal de fichiers dans un partage de fichiers | Aucune limite | Aucune limite |
| Maximum d’E/S par seconde par partage | 10 000 IOPS*, 1 000 IOPS ou 100 demandes en 100ms | 100 000 E/S par seconde |
| Nombre maximal de stratégies d’accès stockées par partage de fichiers | 5 | 5 |
| Débit cible pour un partage de fichier unique | jusqu’à 300 MiB/s*, jusqu’à 60 MiB/s  | Voir les valeurs d’entrée et de sortie du partage de fichiers Premium|
| Sortie maximale pour un partage de fichier unique | Voir le débit de cible du partage de fichiers standard | Jusqu’à 6 204 Mio/s |
| Entrée maximale pour un partage de fichier unique | Voir le débit de cible du partage de fichiers standard | Jusqu’à 4 136 Mio/s |
| Nombre maximal de descripteurs ouverts par fichier ou répertoire | 2 000 descripteurs ouverts | 2 000 descripteurs ouverts |
| Nombre maximal d’instantanés de partage | 200 instantanés de partage | 200 instantanés de partage |
| Longueur maximale du nom de l’objet (répertoires et fichiers) | 2 048 caractères | 2 048 caractères |
| Nombre maximal de caractères des composants de nom de chemin d’accès (dans le chemin d’accès \A\B\C\D, chaque lettre est un composant) | 255 caractères | 255 caractères |

\* Par défaut, la taille des partages de fichiers standard est de 5 Tio. Consultez [Activer et créer des partages de fichiers volumineux](../articles/storage/files/storage-files-how-to-create-large-file-share.md) pour savoir comment effectuer un scale-up des partages de fichiers jusqu’à 100 Tio.
