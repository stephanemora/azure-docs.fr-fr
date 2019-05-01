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
ms.openlocfilehash: 42ab8be45d4086589f0793531003700e7552a440
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744114"
---
**Transferts de données sortants** : [transferts de données sortants](https://azure.microsoft.com/pricing/details/bandwidth/) (données sortant des centres de données Azure) sont facturés en fonction de la bande passante utilisée.

**Transactions** : vous êtes facturé pour le nombre de transactions effectuées sur un disque managé standard. Pour les disques SSD standard, la taille d’unité d’e/s de 256 Kio est utilisée pour le nombre de transactions de comptabilité. Les tailles d’E/S supérieures sont divisées en plusieurs unités d’E/S de 256 Kio. Pour les disques durs Standard, chaque opération d’e/s est considérée comme une transaction unique, indépendamment de la taille d’e/s.

Pour plus d’informations sur la tarification des disques gérés, notamment les coûts de transaction, consultez [Managed Disks tarification](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Frais de réservation de machine virtuelle avec un disque Ultra SSD

Les machines virtuelles Azure peuvent indiquer si elles sont compatibles avec les disques Ultra SSD. Une machine virtuelle compatible avec les disques Ultra alloue une capacité de bande passante dédiée entre l'instance de machine virtuelle de calcul et l'unité d'échelle de stockage de blocs pour optimiser les performances et réduire la latence. L'ajout de cette fonctionnalité sur la machine virtuelle entraîne des frais de réservation qui ne sont imposés que si vous avez activé la fonctionnalité de disque Ultra sur la machine virtuelle sans y joindre de disque Ultra. Lorsqu'un disque Ultra est joint à la machine virtuelle compatible avec les disques Ultra, ces frais ne s'appliquent pas. Les frais sont calculés par processeur virtuel provisionné sur la machine virtuelle.

Pour plus d'informations sur les tarifs des disques Ultra, consultez la [page Tarification des disques Azure](https://azure.microsoft.com/pricing/details/managed-disks/).