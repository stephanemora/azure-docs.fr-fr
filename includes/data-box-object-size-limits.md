---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 2978fff57cfc7ae3dd4629450f573758978058d5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077911"
---
Voici les tailles des objets Azure qui peuvent être écrits. Assurez-vous que tous les fichiers chargés sont en conformité avec ces limites.

| Type d’objet Azure | Limite par défaut                                             |
|-------------------|-----------------------------------------------------------|
| Objet blob de blocs        | ~4.75 Tio                                                 |
| Objet blob de pages         | 8 Tio <br> Chaque fichier chargé dans le format d’objet blob de pages doit être de 512 octets alignés (un multiple entier), sinon le chargement échoue. <br> Les disques VHD et VHDX sont de 512 octets alignés. |
| Azure Files        | 1 Tio                                                      |
| Disques managés     | 4 Tio <br> Pour plus d’informations sur la taille et les limites, consultez : <li>[Objectifs d’évolutivité des disques SSD Standard](../articles/virtual-machines/disks-types.md#standard-ssds)</li><li>[Objectifs d’évolutivité des disques SSD Premium](../articles/virtual-machines/disks-types.md#standard-hdds)</li><li>[Objectifs d’évolutivité des disques HDD Standard](../articles/virtual-machines/disks-types.md#premium-ssds)</li><li>[Tarification et facturation des disques managés](../articles/virtual-machines/disks-types.md#billing)</li>  
