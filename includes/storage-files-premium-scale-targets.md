---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 86bf4911026e46c997469b956f9e7c75c4f17164
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98697900"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites du niveau de partage de fichiers premium supplémentaires

|Domaine  |Cible  |
|---------|---------|
|Augmenter/diminuer la taille minimale    |1 Gio      |
|IOPS de base    |400 + 1 IOPS par Gio, jusqu’à 100 000|
|IOPS en mode rafale    |Max (4 000,3 x IOPS par Gio), jusqu’à 100 000|
|Débit de sortie         |60 Mio/s + 0,06 * Gio configurés        |
|Débit d’entrée| 40 Mio/s + 0,04 * Gio configurés |

#### <a name="file-level-limits"></a>Limites relatives au niveau des fichiers

|Domaine  |Fichier standard  |Fichier Premium  |
|---------|---------|---------|
|Size     |1 Tio         |4 Tio         |
|Nb max. d’IOPS par fichier      |1 000         |Jusqu’à 8 000*         |
|Descripteurs simultanés     |2 000         |2 000         |
|Sortie     |Consultez les valeurs de débit de fichier standard         |300 Mio/s (jusqu’à 1 Gio/s avec la version préliminaire de SMB Multichannel)**         |
|Entrée     |Consultez les valeurs de débit de fichier standard         |200 Mio/s (jusqu’à 1 Gio/s avec la version préliminaire de SMB Multichannel)**        |
|Débit     |Jusqu’à 60 Mio/s         |Voir les valeurs d’entrée et de sortie des fichiers Premium         |

\* <sup> S’applique aux opérations d’E/S en lecture et écriture (généralement des tailles d’E/S plus petites < = 64 ko). Les opérations sur les métadonnées, autres que les lectures et les écritures, peuvent être inférieures. </sup>

\*\* <sup> En fonction des limites du réseau des machines, de la bande passante disponible, des tailles des opérations d’E/S, de la profondeur de la file d’attente et d’autres facteurs. Pour plus d’informations, consultez [Performances de SMB Multichannel](../articles/storage/files/storage-files-smb-multichannel-performance.md). </sup>
