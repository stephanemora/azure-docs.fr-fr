---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542657"
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
|Entrée  |300 Mio/s|      Consultez les valeurs de débit de fichier standard|
|Sortie   |200 Mio/s| Consultez les valeurs de débit de fichier standard|
|Débit| Voir les valeurs d’entrée et de sortie des fichiers Premium| Jusqu’à 60 Mio/s|