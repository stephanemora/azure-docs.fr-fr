---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/27/2021
ms.author: rogarana
ms.openlocfilehash: aedfaf0606547653e88ad6e120f51a542dabdb95
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155158"
---
Vous pouvez attacher un certain nombre de disques de données à une machine virtuelle Azure. En fonction des cibles d’évolutivité et de performances des disques de données d’une machine virtuelle, vous pouvez déterminer le nombre et le type de disque dont vous avez besoin pour répondre à vos exigences de performances et de capacité.

> [!IMPORTANT]
> Pour des performances optimales, limitez le nombre de disques fortement utilisés attachés à la machine virtuelle pour éviter les limitations possibles. Si tous les disques attachés ne sont pas fortement sollicités en même temps, la machine virtuelle peut prendre en charge un plus grand nombre de disques.

**Pour les disques managés Azure :**

Le tableau suivant illustre les limite par défaut et maximales du nombre de ressources par région et par abonnement. Les limites restent les mêmes, quel que soit le nombre de disques chiffrés avec les clés gérées par la plateforme ou les clés gérées par le client. Il n’existe aucune limite au nombre de disques managés, d’instantanés et d’images par groupe de ressources.  

> | Ressource | Limite |
> | --- | --- |
> | Disques managés Standard | 50 000 |
> | Disques managés SSD standard | 50 000 |
> | Disques managés Premium | 50 000 |
> | Captures instantanées Standard_LRS <sup>1</sup> | 75 000 |
> | Captures instantanées Standard_ZRS <sup>1</sup> | 75 000 |
> | Image managée | 50 000 |

<sup>1</sup> Un disque individuel peut avoir 200 captures instantanées de disque complètes au total. Un disque individuel peut également avoir 200 captures instantanées incrémentielles, lesquelles sont comptabilisées séparément des captures instantanées de disque complètes. 

**Pour les comptes Stockage Standard :** un compte Stockage Standard a un taux de requêtes total maximal de 20 000 IOPS. Le nombre d’opérations d’E/S par seconde sur l’ensemble de vos disques de machine virtuelle dans un compte de stockage standard ne doit pas dépasser cette limite.
  
Vous pouvez calculer approximativement le nombre de disques fortement sollicités pris en charge par un seul compte de stockage standard en vous basant sur la limite du taux de demandes. Par exemple, pour une machine virtuelle de niveau de base, le nombre maximal de disques fortement sollicités est d’environ 66, soit 20 000/300 opérations d’E/S par seconde par disque. Pour une machine virtuelle de niveau standard, le nombre maximal de disques fortement sollicités est d’environ 40, soit 20 000/500 IOPS par disque. 

**Pour les comptes de stockage premium :** un compte de stockage premium a un débit total maximum de 50 Gbits/s. Le débit total sur l'ensemble de vos disques de machine virtuelle ne doit pas dépasser cette limite.

