---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0d771f03f9f71151ef25140148d4dd4daf3d46ec
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443410"
---
**Transferts de données sortants** : [transferts de données sortants](https://azure.microsoft.com/pricing/details/bandwidth/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

**Transactions** : vous êtes facturé pour le nombre de transactions effectuées sur un disque managé standard. Azure facture 0,0036 $ par tranche de 100 000 transactions pour les HDD standard. Les transactions comprennent les opérations de lecture et d’écriture pour le stockage.

Les disques SSD Standard utilisent une taille d'unité d'E/S de 256 Ko. Si la taille des données transférées est inférieure à 256 Ko, elles sont considérées comme 1 unité d'E/S. Les tailles d’E/S supérieures sont divisées en plusieurs unités d’E/S de 256 Ko. Par exemple, une E/S de 1 100 Ko correspond à cinq unités d’E/S.

Les transactions associées aux disques managés Premium ne sont pas facturées.

Pour plus d’informations sur la tarification d’Azure Managed Disks, consultez la page [Managed Disks Tarification](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Frais de réservation de machine virtuelle avec un disque Ultra SSD

Les machines virtuelles Azure peuvent indiquer si elles sont compatibles avec les disques Ultra SSD. Une machine virtuelle compatible avec les disques Ultra alloue une capacité de bande passante dédiée entre l'instance de machine virtuelle de calcul et l'unité d'échelle de stockage de blocs pour optimiser les performances et réduire la latence. L'ajout de cette fonctionnalité sur la machine virtuelle entraîne des frais de réservation qui ne sont imposés que si vous avez activé la fonctionnalité de disque Ultra sur la machine virtuelle sans y joindre de disque Ultra. Lorsqu'un disque Ultra est joint à la machine virtuelle compatible avec les disques Ultra, ces frais ne s'appliquent pas. Les frais sont calculés par processeur virtuel provisionné sur la machine virtuelle.

Pour plus d'informations sur les tarifs des disques Ultra, consultez la [page Tarification des disques Azure](https://azure.microsoft.com/pricing/details/managed-disks/).