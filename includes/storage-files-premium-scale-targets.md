---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057836"
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

|Domaine  |Fichier standard  |Fichier Premium  |
|---------|---------|---------|
|Size     |1 Tio         |4 Tio         |
|Nb max. d’IOPS par fichier      |1 000         |5 000         |
|Descripteurs simultanés     |2 000         |2 000         |
|Sortie     |Consultez les valeurs de débit de fichier standard         |300 Mio/s         |
|Entrée     |Consultez les valeurs de débit de fichier standard         |200 Mio/s         |
|Débit     |Jusqu’à 60 Mio/s         |Voir les valeurs d’entrée et de sortie des fichiers Premium         |