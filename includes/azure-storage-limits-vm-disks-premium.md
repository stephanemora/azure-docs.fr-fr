---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80334762"
---
**Disques de machine virtuelle non gérés Premium : limites par compte**

| Ressource | Limite |
| --- | --- |
| Capacité totale des disques par compte |35 To |
| Capacité totale des instantanés par compte |10 To |
| Bande passante maximale par compte (entrée + sortie)<sup>1</sup> |<=50 Gbits/s |

<sup>1</sup>*Entrée* désigne toutes les données issues de requêtes envoyées à un compte de stockage. *Sortie* désigne toutes les données issues des réponses reçues depuis un compte de stockage.

**Disques de machine virtuelle non gérés Premium : limites par disque**

| Type de disque de stockage Premium | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| Taille du disque |128 Gio |512 Gio |1 024 Gio (1 To) |2 048 Gio (2 To)|4 095 Gio (4 To)|
| Maximum d’E/S par seconde par disque |500 |2 300 |5 000 |7 500 |7 500 |
| Débit maximal par disque |100 Mo/s | 150 Mo/s |200 Mo/s |250 Mo/s |250 Mo/s |
| Nombre maximal de disques par compte de stockage |280 |70 |35 | 17 | 8 |

**Disques de machine virtuelle non gérés Premium : limites par machine virtuelle**

| Ressource | Limite |
| --- | --- |
| Maximum d’E/S par seconde par machine virtuelle |80 000 E/S par seconde avec la machine virtuelle GS5 |
| Débit maximal par machine virtuelle |2 000 Mo/s avec la machine virtuelle GS5 |

