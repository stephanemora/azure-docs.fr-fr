---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: 09b4f94db3464943a8367bfb3ca89f9a88446193
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553711"
---
Vous pouvez attacher un nombre de disques de données à une machine virtuelle Azure. Basé sur les cibles d’évolutivité et performances pour les disques de données d’une machine virtuelle, vous pouvez déterminer le nombre et le type de disque dont vous avez besoin pour répondre à vos performances en matière de capacité.

> [!IMPORTANT]
> Pour des performances optimales, limitez le nombre de disques fortement utilisés attachés à la machine virtuelle pour éviter les limitations possibles. Si tous les disques attachés ne sont pas fortement sollicités en même temps, la machine virtuelle peut prendre en charge un plus grand nombre de disques.

* **Pour Azure des disques gérés :** 

> | Ressource | Limite par défaut | Limite maximale |
> | --- | --- | --- |
> | Disques gérés Standard | 10 000 | 50 000 |
> | Disques managés SSD Standard | 10 000 | 50 000 |
> | Managed Disks Premium | 10 000 | 50 000 |
> | Standard_LRS instantanés | 10 000 | 50 000 |
> | Standard_ZRS instantanés | 10 000 | 50 000 |
> | Image managée | 10 000 | 50 000 |

* **Pour les comptes de stockage Standard :** Un compte de stockage Standard a un taux de demandes total maximal de 20 000 e/s. Les total des IOPS sur l’ensemble de vos disques de machine virtuelle dans un compte de stockage Standard ne doit pas dépasser cette limite.
  
    Vous pouvez calculer approximativement le nombre de disques fortement sollicités pris en charge par un compte de stockage Standard unique basé sur la limite de taux de demande. Par exemple, pour un niveau de base machine virtuelle, le nombre maximal de disques fortement sollicités est environ 66, c'est-à-dire les e/s de 20 000/300 par disque. Le nombre maximal de disques fortement sollicités pour une machine virtuelle de niveau Standard est d’environ 40, qui est de 20 000/500 IOPS par disque. 

* **Pour les comptes de stockage Premium :** Un compte de stockage Premium a un débit total maximum de 50 Gbit/s. Le débit total sur l'ensemble de vos disques de machine virtuelle ne doit pas dépasser cette limite.

