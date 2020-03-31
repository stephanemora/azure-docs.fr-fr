---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180046"
---
#### <a name="additional-premium-file-share-level-limits"></a>Limites du niveau de partage de fichiers premium supplémentaires

|Domaine  |Cible  |
|---------|---------|
|Augmenter/diminuer la taille minimale    |1 Gio      |
|IOPS de base    |1 IOPS par Gio (jusqu’à 100 000)|
|IOPS en mode rafale    |3 IOPS par Gio (jusqu’à 100 000)|
|Débit de sortie         |60 Mio/s + 0,06 * Gio configurés        |
|Débit d’entrée| 40 Mio/s + 0,04 * Gio configurés |

#### <a name="file-level-limits"></a>Limites relatives au niveau des fichiers

|Domaine  |Fichier Premium  |Fichier standard |
|---------|---------|---------|
|Size                  |1 Tio         |1 Tio|
|Nb max. d’IOPS par fichier     |5 000         |1 000|
|Descripteurs simultanés    |2 000         |2 000|
|Sortie  |300 Mio/s|      Consultez les valeurs de débit de fichier standard|
|Entrée  |200 Mio/s| Consultez les valeurs de débit de fichier standard|
|Débit| Voir les valeurs d’entrée et de sortie des fichiers Premium| Jusqu’à 60 Mio/s|
