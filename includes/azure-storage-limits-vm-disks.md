---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: e89676505bc211d01a4327f8816a048218e579fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829168"
---
Une machine virtuelle Azure prend en charge l’attachement d’un certain nombre de disques de données. En fonction des cibles d’évolutivité et de performances des disques de données d’une machine virtuelle, vous pouvez déterminer le nombre et le type de disque dont vous avez besoin pour répondre à vos exigences de performances et de capacité.

> [!IMPORTANT]
> Pour des performances optimales, limitez le nombre de disques fortement utilisés attachés à la machine virtuelle pour éviter les limitations possibles. Si tous les disques attachés ne sont pas fortement sollicités en même temps, la machine virtuelle peut prendre en charge un plus grand nombre de disques.

**Pour les disques managés Azure :**

Le tableau suivant illustre les limite par défaut et maximales du nombre de ressources par région et par abonnement. Il n’existe aucune limite au nombre de disques managés, d’instantanés et d’images par groupe de ressources.  

> | Ressource | Limite |
> | --- | --- |
> | Disques managés Standard | 50 000 |
> | Disques managés SSD standard | 50 000 |
> | Disques managés Premium | 50 000 |
> | Standard_LRS snapshots | 50 000 |
> | Standard_ZRS snapshots | 50 000 |
> | Image managée | 50 000 |

**Pour les comptes de stockage standard :** un compte de stockage standard a un taux de requêtes total maximal de 20 000 opérations d’E/S par seconde. Le nombre d’opérations d’E/S par seconde sur l’ensemble de vos disques de machine virtuelle dans un compte de stockage standard ne doit pas dépasser cette limite.
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**Pour les comptes de stockage Premium :** un compte de stockage Premium a un débit total maximum de 50 Gbits/s. Le débit total sur l'ensemble de vos disques de machine virtuelle ne doit pas dépasser cette limite.

