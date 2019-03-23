---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88a4110d68dc8aa921d647f90de654d2ebb4e17d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395540"
---
## <a name="premium-ssd"></a>SSD Premium

Les disques SSD Premium Azure offrent une prise en charge très performante et une faible latence pour les machines virtuelles avec des charges de travail qui utilisent beaucoup d’entrée/sortie (E/S). Pour tirer parti de la vitesse et des performances des disques de stockage Premium, vous pouvez migrer les disques de machines virtuelles existantes vers des disques SSD Premium. Les disques SSD Premium sont adaptés aux applications de production critiques.

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="standard-ssd"></a>SSD Standard

Les disques SSD Azure Standard constituent une option de stockage économique optimisée pour les charges de travail nécessitant des performances cohérentes à des niveaux d’IOPS bas. Les disques SSD Standard offrent une bonne expérience de niveau d’entrée pour ceux qui souhaitent migrer vers le cloud, en particulier si vous rencontrez des problèmes de variation avec les charges de travail exécutées sur vos solutions HDD locales. Les disques SSD standard offrent une meilleure disponibilité, cohérence, fiabilité et latence par rapport aux disques HDD. Les disques SSD Standard sont adaptés aux serveurs web, serveurs d’applications avec peu d’IOPS, applications d’entreprise peu utilisées et charges de travail Dev/Test.

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

## <a name="standard-hdd"></a>HDD Standard

Les disques HDD standard Azure offrent une prise en charge fiable et économique des disques des machines virtuelles exécutant des charges de travail non sensibles aux latences. Il prend également en charge les objets blob, les tables, les files d’attente et les fichiers. Dans le cadre d’un stockage Standard, les données sont stockées sur des disques durs. Lorsque vous utilisez des machines virtuelles, vous pouvez utiliser des disques SSD et HDD Standard pour les scénarios Dev/Test et les charges de travail moins critiques. Le stockage Standard est disponible dans toutes les régions Azure.

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>Facturation

Lorsque vous utilisez des disques managés, les considérations de facturation suivantes s’appliquent :

- Type de disque
- Taille des disques managés
- Instantanés
- Transferts de données sortantes
- Nombre de transactions

**Taille des disques managés** : les disques managés sont facturés selon la taille provisionnée. Azure fait correspondre la taille provisionnée (arrondie à la valeur supérieure) à la taille de disque offerte la plus proche. Pour plus d’informations sur les tailles de disque proposées, consultez les tableaux précédents. Chaque disque correspond à une offre de taille de disque configurée prise en charge, et est facturé en conséquence. Par exemple, si vous avez provisionné un disque SSD Standard de 200 Gio, celui-ci correspond à l’offre de taille de disque E15 (256 Gio). La facturation de n’importe quel disque configuré est calculée au prorata horaire sur la base du tarif mensuel de l’offre de stockage Premium. Par exemple, si vous provisionnez un disque E10 et le supprimez au bout de 20 heures, l’offre E10 vous est facturée au prorata de 20 heures. Le montant facturé est indépendant de la quantité de données écrites sur le disque.

**Instantanés** : Les instantanés facturés en fonction de la taille utilisée. Par exemple, si vous créez une capture instantanée d’un disque managé avec une capacité approvisionnée de 64 Gio et une taille des données utilisées réelle de 10 Gio, l'instantané est facturé uniquement pour la taille des données utilisées de 10 Gio.