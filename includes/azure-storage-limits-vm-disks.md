---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 2936fd318f08c74675f7e8b382c861f4a28319fc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148162"
---
Vous pouvez attacher un nombre de disques de données à une machine virtuelle Azure. Basé sur les cibles d’évolutivité et performances pour les disques de données d’une machine virtuelle, vous pouvez déterminer le nombre et le type de disque dont vous avez besoin pour répondre à vos performances en matière de capacité.

> [!IMPORTANT]
> Pour des performances optimales, limitez le nombre de disques fortement utilisés attachés à la machine virtuelle pour éviter les limitations possibles. Si tous les disques attachés ne sont pas fortement sollicités en même temps, la machine virtuelle peut prendre en charge un plus grand nombre de disques.

**Pour Azure des disques gérés :**

Le tableau suivant illustre les par défaut et les limites maximales du nombre de ressources par région et par abonnement

> | Ressource | Limite par défaut  | Limite maximale |
> | --- | --- | --- |
> | Disques managés Standard | 25 000 | 50 000 |
> | Disques managés SSD Standard | 25 000 | 50 000 |
> | Managed Disks Premium | 25 000 | 50 000 |
> | Standard_LRS instantanés | 25 000 | 50 000 |
> | Standard_ZRS instantanés | 25 000 | 50 000 |
> | Image managée | 25 000 | 50 000 |

* **Pour les comptes de stockage Standard :** Un compte de stockage Standard a un taux de demandes total maximal de 20 000 e/s. Les total des IOPS sur l’ensemble de vos disques de machine virtuelle dans un compte de stockage Standard ne doit pas dépasser cette limite.
  
    Vous pouvez calculer approximativement le nombre de disques fortement sollicités pris en charge par un compte de stockage Standard unique basé sur la limite de taux de demande. Par exemple, pour un niveau de base machine virtuelle, le nombre maximal de disques fortement sollicités est environ 66, c'est-à-dire les e/s de 20 000/300 par disque. Le nombre maximal de disques fortement sollicités pour une machine virtuelle de niveau Standard est d’environ 40, qui est de 20 000/500 IOPS par disque. 

* **Pour les comptes de stockage Premium :** Un compte de stockage Premium a un débit total maximum de 50 Gbit/s. Le débit total sur l'ensemble de vos disques de machine virtuelle ne doit pas dépasser cette limite.

