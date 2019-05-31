---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c7b73cad200666db9e926d8e808eaa4a8dccffb2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248905"
---
## <a name="premium-ssd"></a>SSD Premium

Les disques SSD Premium Azure offrent une prise en charge très performante et une faible latence pour les machines virtuelles avec des charges de travail qui utilisent beaucoup d’entrée/sortie (E/S). Pour tirer parti de la vitesse et des performances des disques de stockage Premium, vous pouvez migrer les disques de machines virtuelles existantes vers des disques SSD Premium. Les disques SSD Premium sont adaptés aux applications de production critiques. Disques SSD Premium utilisable uniquement avec les séries de machines virtuelles qui sont compatibles avec le stockage de premium.

Pour en savoir plus sur les types de machines virtuelles individuelles et les tailles dans Azure pour Windows, notamment les tailles premium stockage compatibles, consultez [tailles de machine virtuelle Windows](../articles/virtual-machines/windows/sizes.md). Pour plus d’informations sur des types de machines virtuelles et tailles dans Azure pour Linux, notamment les tailles premium compatible avec le stockage, consultez [Linux VM sizes](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Lorsque vous configurez un disque de stockage Premium, contrairement au stockage standard, la capacité, les E/S par seconde et le débit de ce disque sont assurés. Par exemple, si vous créez un disque P50, Azure configure une capacité de stockage de 4 095 Go, 7 500 E/S par seconde et un débit de 250 Mo/s pour ce disque. Votre application peut utiliser tout ou partie de la capacité et des performances. Les disques SSD Premium sont conçues pour fournir des temps de latence faible en quelques millisecondes et cibler les IOPS et débit décrit dans le précédent table 99,9 % du temps.

### <a name="transactions"></a>Transactions

Pour les disques SSD premium, chaque opération d’e/s inférieure ou égale à 256 Kio du débit est considéré comme une seule opération d’e/s. Plus de 256 Kio des opérations d’e/s de débit sont considérés comme plusieurs e/s de taille 256 Kio.

## <a name="standard-ssd"></a>SSD Standard

Les disques SSD Azure Standard constituent une option de stockage économique optimisée pour les charges de travail nécessitant des performances cohérentes à des niveaux d’IOPS bas. Les disques SSD Standard offrent une bonne expérience de niveau d’entrée pour ceux qui souhaitent migrer vers le cloud, en particulier si vous rencontrez des problèmes de variation avec les charges de travail exécutées sur vos solutions HDD locales. Par rapport aux disques durs standards, SSDs standards offrent une meilleure disponibilité, la cohérence, la fiabilité et la latence. Les disques SSD Standard sont adaptés aux serveurs web, serveurs d’applications avec peu d’IOPS, applications d’entreprise peu utilisées et charges de travail Dev/Test. Comme les disques durs standards, SSDs standards sont disponibles sur toutes les machines virtuelles de Azure.

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

SSDs standards sont conçus pour fournir des latences de quelques millisecondes à un chiffre et les e/s et le débit dans les limites décrites dans le précédent tableau 99 % du temps. E/s et le débit réel peuvent varier parfois selon les modèles de trafic. Les disques SSD Standard offrent des performances plus cohérentes que les disques HDD avec une latence plus faible.

### <a name="transactions"></a>Transactions

Pour les disques SSD standard, chaque opération d’e/s inférieure ou égale à 256 Kio du débit est considéré comme une seule opération d’e/s. Plus de 256 Kio des opérations d’e/s de débit sont considérés comme plusieurs e/s de taille 256 Kio. Ces transactions ont un impact sur la facturation.

## <a name="standard-hdd"></a>HDD Standard

Les disques HDD standard Azure offrent une prise en charge fiable et économique des disques des machines virtuelles exécutant des charges de travail non sensibles aux latences. Dans le cadre d’un stockage Standard, les données sont stockées sur des disques durs. Les disques de latence, les IOPS et débit de Standard du disque dur peuvent varier plus largement par rapport aux disques SSD. Lorsque vous travaillez avec des machines virtuelles, vous pouvez utiliser des disques HDD standard pour les scénarios de développement/test et moins de charges de travail critiques. Disques durs standards sont disponibles dans toutes les régions Azure et peuvent être utilisées avec toutes les machines virtuelles de Azure.

### <a name="disk-size"></a>Taille du disque
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transactions

Pour les disques durs Standard, chaque opération d’e/s est considérée comme une transaction unique, indépendamment de la taille d’e/s. Ces transactions ont un impact sur la facturation.

## <a name="billing"></a>Facturation

Lorsque vous utilisez des disques managés, les considérations de facturation suivantes s’appliquent :

- Type de disque
- Taille des disques managés
- Instantanés
- Transferts de données sortantes
- Nombre de transactions

**Taille des disques managés** : les disques managés sont facturés selon la taille provisionnée. Azure fait correspondre la taille provisionnée (arrondie à la valeur supérieure) à la taille de disque offerte la plus proche. Pour plus d’informations sur les tailles de disque proposées, consultez les tableaux précédents. Chaque disque correspond à une offre de taille de disque configurée prise en charge, et est facturé en conséquence. Par exemple, si vous avez provisionné un disque SSD Standard de 200 Gio, celui-ci correspond à l’offre de taille de disque E15 (256 Gio). La facturation de n’importe quel disque configuré est calculée au prorata horaire sur la base du tarif mensuel de l’offre de stockage Premium. Par exemple, si vous provisionnez un disque E10 et le supprimez au bout de 20 heures, l’offre E10 vous est facturée au prorata de 20 heures. Le montant facturé est indépendant de la quantité de données écrites sur le disque.

**Instantanés** : Les instantanés facturés en fonction de la taille utilisée. Par exemple, si vous créez une capture instantanée d’un disque managé avec une capacité approvisionnée de 64 Gio et une taille des données utilisées réelle de 10 Gio, l'instantané est facturé uniquement pour la taille des données utilisées de 10 Gio.
