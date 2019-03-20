---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553567"
---
**Disques Premium de machine virtuelle non gérée : Limites par compte**

| Ressource | Limite par défaut |
| --- | --- |
| Capacité totale des disques par compte |35 To |
| Capacité totale des instantanés par compte |10 To |
| Bande passante maximale par compte (entrée + sortie)<sup>1</sup> |<=50 Gbits/s |

<sup>1</sup>*entrée* fait référence à toutes les données à partir des demandes qui sont envoyées à un compte de stockage. *Sortie* fait référence à toutes les données à partir des réponses sont reçues d’un compte de stockage.

**Disques Premium de machine virtuelle non gérée : Limites par disque**

| Type de disque de stockage Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Taille du disque |128 Go |512 Go |1 024 Gio (1 To) |2 048 Gio (2 To)|4 095 Gio (4 To)|
| Nombre maximal d’IOPS par disque |500 |2 300 |5 000 |7 500 |7 500 |
| Débit maximal par disque |100 Mo/s | 150 Mo/s |200 Mo/s |250 Mo/s |250 Mo/s |
| Nombre maximal de disques par compte de stockage |280 |70 |35 | 17 | 8 |

**Disques Premium de machine virtuelle non gérée : Limites par machine virtuelle**

| Ressource | Limite par défaut |
| --- | --- |
| Nombre maximal d’IOPS par machine virtuelle |80 000 E/S par seconde avec la machine virtuelle GS5 |
| Débit maximal par machine virtuelle |2 000 Mo/s avec la machine virtuelle GS5 |

