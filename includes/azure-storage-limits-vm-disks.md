---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 8b25d2395811a2197aff6d653c5038a4380021e9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "68669641"
---
Une machine virtuelle Azure prend en charge l’attachement d’un certain nombre de disques de données. En fonction des cibles d’évolutivité et de performances des disques de données d’une machine virtuelle, vous pouvez déterminer le nombre et le type de disque dont vous avez besoin pour répondre à vos exigences de performances et de capacité.

> [!IMPORTANT]
> Pour des performances optimales, limitez le nombre de disques fortement utilisés attachés à la machine virtuelle pour éviter les limitations possibles. Si tous les disques attachés ne sont pas fortement sollicités en même temps, la machine virtuelle peut prendre en charge un plus grand nombre de disques.

**Pour les disques managés Azure :**

Le tableau suivant illustre les limite par défaut et maximales du nombre de ressources par région et par abonnement. Il n’existe aucune limite au nombre de disques managés, d’instantanés et d’images par groupe de ressources.  

> | Ressource | Limite par défaut  | Limite maximale |
> | --- | --- | --- |
> | Disques managés Standard | 50 000 | 50 000 |
> | Disques managés SSD standard | 50 000 | 50 000 |
> | Disques managés Premium | 50 000 | 50 000 |
> | Standard_LRS snapshots | 50 000 | 50 000 |
> | Standard_ZRS snapshots | 50 000 | 50 000 |
> | Image managée | 50 000 | 50 000 |

* **Pour les comptes de stockage standard :** un compte de stockage standard a un taux de requêtes total maximal de 20 000 opérations d’E/S par seconde. Le nombre d’opérations d’E/S par seconde sur l’ensemble de vos disques de machine virtuelle dans un compte de stockage standard ne doit pas dépasser cette limite.
  
    Vous pouvez calculer approximativement le nombre de disques fortement sollicités pris en charge par un seul compte de stockage standard en vous basant sur la limite du taux de demandes. Par exemple, pour une machine virtuelle de niveau de base, le nombre maximal de disques fortement sollicités est d’environ 66, soit 20 000/300 opérations d’E/S par seconde par disque. Pour une machine virtuelle de niveau standard, le nombre maximal de disques fortement sollicités est d’environ 40, soit 20 000/500 IOPS par disque. 

* **Pour les comptes de stockage Premium :** un compte de stockage Premium a un débit total maximum de 50 Gbits/s. Le débit total sur l'ensemble de vos disques de machine virtuelle ne doit pas dépasser cette limite.

