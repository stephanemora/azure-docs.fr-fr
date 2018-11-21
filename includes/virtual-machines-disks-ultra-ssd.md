---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 050308e1c8de160f1671ded991e550087299ae2f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285798"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Disques managés Ultra SSD (préversion) pour les charges de travail de machine virtuelle Azure

Les disques Ultra SSD d’Azure (préversion) fournissent un stockage de disque avec un haut débit, un nombre d’IOPS élevé et une faible latence homogène pour les machines virtuelles IaaS Azure. Cette nouvelle offre fournit des performances optimales aux mêmes niveaux de disponibilité que nos offres de disques existantes. Entre autres avantages, Ultra SSD permet de changer dynamiquement les performances de disque en fonction de vos charges de travail sans avoir à redémarrer les machines virtuelles. L’offre Ultra SSD est adaptée aux charges de travail qui consomment beaucoup de données comme SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions.

## <a name="ultra-ssd-features"></a>Caractéristiques des disques Ultra SSD

**Disques managés** : Les disques Ultra SSD sont seulement disponibles sous forme de disques managés. Les disques Ultra SSD ne peuvent pas être déployés comme des disques non managés ou des objets blob de pages. Quand vous créez un disque managé, vous définissez le type de référence de disque sur UltraSSD_LRS et indiquez la taille de disque, le nombre d’IOPS et le débit dont vous avez besoin, puis Azure crée et gère le disque pour vous.  

**Machines virtuelles** : Les disques Ultra SSD sont conçus pour fonctionner avec toutes les références de machine virtuelle Azure compatibles avec les SSD Premium. Cela étant, dans la préversion, les tailles de machines virtuelles sont limitées aux instances de machines virtuelles ES/DS v3.

**Configuration dynamique des performances** : Les disques Ultra SSD vous permettent de changer dynamiquement les performances (IOPS et débit) du disque conformément aux besoins de vos charges de travail, sans avoir à redémarrer vos machines virtuelles.

## <a name="scalability-and-performance-targets"></a>Cibles de performance et d’évolutivité

Quand vous provisionnez un disque Ultra SSD, vous pouvez configurer de manière indépendante la capacité et les performances du disque. Les disques Ultra SSD se déclinent en plusieurs tailles fixes de 4 Gio à 64 Tio, et présentent un modèle de configuration de performances flexible qui vous permet de configurer les IOPS et le débit de manière indépendante. Les disques Ultra SSD peuvent uniquement être utilisés comme disques de données. Nous vous recommandons d’utiliser des disques SSD Premium comme disques de système d’exploitation.

Voici certaines fonctionnalités clés des disques Ultra SSD :

- Capacité du disque : les disques Ultra SSD existent dans différentes tailles de 4 Gio à 64 Tio.
- IOPS du disque : les disques Ultra SSD prennent en charge des limites d’IOPS de 300 IOPS/Gio, avec un maximum de 160 K IOPS par disque. Pour atteindre le nombre d’IOPS provisionné, vérifiez que le nombre d’IOPS du disque sélectionné est inférieur au nombre d’IOPS de la machine virtuelle. Le nombre minimal d’IOPS du disque est 100.
- Débit du disque : Avec les disques Ultra SSD, la limite de débit d’un seul disque est 256 Kio/s pour chacune IOPS provisionné, avec un maximum de 2 000 Mbits/s par disque (où 1 Mbit/s = 10^6 octets par seconde). Le débit minimal du disque est 1 Mio.

Le tableau suivant récapitule les différentes configurations prises en charge pour les différentes tailles de disque :  

### <a name="ultra-ssd-managed-disk-offerings"></a>Offres de disques managés Ultra SSD

|Taille de disque (Gio)  |Nombre limite d’IOPS  |Débit limite (Mbits/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2 400         |600         |
|16     |4 800         |1,200         |
|32     |9 600         |2 000         |
|64     |19 200         |2 000         |
|128     |38 400         |2 000         |
|256     |76 800         |2 000         |
|512     |80 000         |2 000         |
|1 024 à 65 536 (dans cette plage, les tailles augmentent par incréments de 1 Tio)     |160 000         |2 000         |

## <a name="pricing-and-billing"></a>Tarification et facturation

Lorsque vous utilisez des disques Ultra SSD, les règles de facturation suivantes s’appliquent :

- Taille des disques managés
- Nombre d’IOPS provisionnées pour le disque managé
- Débit provisionné pour le disque managé
- Frais de réservation de machine virtuelle avec un disque Ultra SSD

### <a name="managed-disk-size"></a>Taille des disques managés

Les disques managés sont facturés en fonction des tailles de machine virtuelle choisie lors de la configuration d'une nouvelle machine virtuelle Azure. Azure fait correspondre la taille provisionnée (arrondie à la valeur supérieure) à l’offre de taille de disque la plus proche. Pour plus d’informations sur les tailles de disque proposées, consultez le tableau dans la section Objectifs de performance et de scalabilité ci-dessus. Chaque disque correspond à une taille de disque provisionnée prise en charge et est facturé en conséquence à l’heure. Par exemple, si vous avez provisionné un disque Ultra SSD de 200 Gio et que vous l’avez supprimé au bout de 20 heures, il est mappé à l’offre de taille de disque de 256 Gio et vous êtes facturé 256 Gio pendant 20 heures. Cette facturation est basée sur la consommation par heure de calcul, indépendamment du volume de données réellement écrites sur le disque.

### <a name="managed-disk-provisioned-iops"></a>Nombre d’IOPS provisionnées pour le disque managé

Le nombre d’IOPS est le nombre de demandes que votre application envoie aux disques par seconde. Une opération d’entrée/sortie peut être séquentielle ou aléatoire et consister en lectures ou en écritures. Selon la taille du disque ou le nombre de disques attachés à la machine virtuelle, le nombre moyen d’IOPS est facturé sur une base horaire. Pour plus d’informations sur les nombres d’IOPS de disque proposés, consultez le tableau de la section Cibles de performance et d’évolutivité ci-dessus.

### <a name="managed-disk-provisioned-throughput"></a>Débit provisionné pour le disque managé

Le débit est la quantité de données que votre application envoie aux disques dans un intervalle spécifié, mesuré en octets/seconde. Si votre application effectue de grosses opérations d’entrée/sortie, elle a besoin d’un débit élevé.  

La formule suivante illustre la relation entre le débit et le nombre d’IOPS : IOPS x taille d’E/S = Débit

Par conséquent, il est important de déterminer les valeurs optimales de débit et d’E/S par seconde dont a besoin votre application. Lorsque vous essayez d’optimiser une de ces valeurs, l’autre est également affectée. Nous vous recommandons de commencer par un débit correspondant à la taille d’E/S de 16 Kio et de l’ajuster si vous avez besoin de plus de débit.

Pour plus d’informations sur le débit de disque pris en charge sur les disques Ultra SSD, consultez le tableau de la section Cibles de performance et d’évolutivité ci-dessus. Tout comme la taille et le nombre d’IOPS du disque, le débit provisionné est facturé à l’heure par nombre de Mbits/s provisionnés.

### <a name="ultra-ssd-vm-reservation-fee"></a>Frais de réservation de machine virtuelle avec un disque Ultra SSD

Nous ajoutons une fonctionnalité sur la machine virtuelle qui indique si votre machine virtuelle est compatible avec les disques Ultra SSD. Une machine virtuelle compatible Ultra SSD alloue une capacité de bande passante dédiée entre l’instance de machine virtuelle de calcul et l’unité d’échelle de stockage de blocs pour optimiser les performances et réduire la latence. L’ajout de cette fonctionnalité sur la machine virtuelle entraîne des frais de réservation imposés uniquement si vous activez la fonctionnalité Ultra SSD sur la machine virtuelle sans lui attacher de disque Ultra SSD. Quand un disque Ultra SSD est attaché à la machine virtuelle compatible Ultra SSD, ces frais ne s’appliquent pas. Les frais sont calculés par processeur virtuel provisionné sur la machine virtuelle.

Consultez la [page de tarifs des disques Azure](https://azure.microsoft.com/pricing/details/managed-disks/) pour connaître les détails tarifaires des nouveaux disques Ultra SSD disponibles en préversion limitée.

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Limitations et étendue de la préversion de la fonctionnalité Ultra SSD

Dans la préversion, les disques Ultra SSD ont les limites suivantes :

- Ils sont initialement pris en charge dans la région USA Est 2 dans une seule zone de disponibilité  
- Ils peuvent être utilisés seulement avec des zones de disponibilité (les groupes à haute disponibilité et les déploiements de machine virtuelle individuelle en dehors des zones n’ont pas la possibilité d’attacher un disque Ultra SSD)
- Ils sont pris en charge seulement sur les machines virtuelles ES/DS v3
- Ils sont disponibles seulement comme des disques de données et prennent en charge uniquement une taille de secteur physique de 4 k  
- Ils peuvent être créés seulement comme des disques vides  
- Actuellement, ils peuvent être déployés uniquement à l’aide de modèles Resource Manager, de l’interface CLI et du SDK Python.
- Ils ne prennent pas (encore) en charge les instantanés de disque, les images de machine virtuelle, les groupes à haute disponibilité, les groupes de machines virtuelles identiques et Azure Disk Encryption.
- Ils ne prennent pas (encore) en charge l’intégration à la Sauvegarde Azure ou à Azure Site Recovery.
- Comme pour la  [plupart des préversions](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), cette fonctionnalité ne doit pas être utilisée pour les charges de travail de production avant la mise en disponibilité générale.
