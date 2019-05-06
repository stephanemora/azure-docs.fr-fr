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
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564340"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Disques Ultra (version préliminaire) des disques pour les charges de travail de machine virtuelle Azure gérés

Disques ultra Azure (version préliminaire) offre un débit élevé, nombre élevé d’IOPS et le stockage de disque d’une latence faible homogène pour les machines virtuelles IaaS Azure. Cette nouvelle offre fournit des performances optimales aux mêmes niveaux de disponibilité que nos offres de disques existantes. Autres avantages des disques ultra incluent la possibilité de modifier dynamiquement les performances du disque, ainsi que vos charges de travail sans avoir à redémarrer vos machines virtuelles. Disques Ultra sont adaptées aux charges de travail gourmandes en données telles que SAP HANA, les bases de données de niveau supérieur et les charges de travail nécessitant beaucoup de la transaction.

## <a name="ultra-disk-features"></a>Fonctionnalités de disque ultra

**Disques managés** : Ultra disques sont uniquement disponibles en tant que disques gérés. Disques Ultra ne peut pas être déployés comme un disque non géré ou un objet blob de pages. Lors de la création d’un disque géré, vous spécifiez la référence (SKU) disque tapez comme UltraSSD_LRS et indiquer la taille du disque, les e/s, et vous avez besoin de débit, Azure crée et gère le disque pour vous.  

**Machines virtuelles** : Disques Ultra sont conçus pour fonctionner avec tous les Premium SSD références SKU de Machine virtuelle Azure est activée ; Toutefois, comme il est actuellement en version préliminaire, les machines virtuelles sont dimensionnés comme v3 d’ES/DS.

**Configuration dynamique des performances** : Disques Ultra permettent de modifier dynamiquement les performances (IOPS et débit) du disque, ainsi que les besoins de votre charge de travail sans avoir à redémarrer vos machines virtuelles.

## <a name="scalability-and-performance-targets"></a>Cibles de performance et d’évolutivité

Lorsque vous configurez un disque ultra, avoir la possibilité de configurer indépendamment la capacité et les performances du disque. Disques Ultra sont en plusieurs tailles fixes à partir de 4 Go jusqu'à 64 TIO et un modèle de configuration flexibles des performances qui vous permet de configurer de façon indépendante IOPS et débit. Disques Ultra pouvant uniquement être utilisées comme des disques de données. Nous vous recommandons d’utiliser des disques SSD Premium comme disques de système d’exploitation.

Certaines fonctionnalités clées de disques ultra sont :

- Capacité du disque : Disques Ultra offre une plage de tailles de disque différent à partir de 4 Go jusqu'à 64 To.
- IOPS du disque : Disques Ultra prennent en charge les limites d’IOPS des Gio/300 e/s, avec un maximum de 160 Ko IOPS par disque. Pour atteindre le nombre d’IOPS provisionné, vérifiez que le nombre d’IOPS du disque sélectionné est inférieur au nombre d’IOPS de la machine virtuelle. Le nombre minimal d’IOPS du disque est 100.
- Débit du disque : Avec des disques ultra, la limite de débit d’un seul disque est 256 Kio/s pour chacune configurée e/s, avec un maximum de 2000 les Mo/s par disque (où Mo/s = 10 ^ 6 octets par seconde). Le débit minimal du disque est 1 Mio.

Le tableau suivant récapitule les différentes configurations prises en charge pour les différentes tailles de disque :  

### <a name="ultra-disks-managed-disk-offerings"></a>Ultra disques gérés de disque

|Taille de disque (Gio)  |Nombre limite d’IOPS  |Débit limite (Mo/s)  |
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

Lorsque vous utilisez des disques ultra, considérations de facturation suivantes seront appliquées :

- Taille des disques managés
- Nombre d’IOPS provisionnées pour le disque managé
- Débit provisionné pour le disque managé
- Frais de réservation de machine virtuelle de disque ultra

### <a name="managed-disk-size"></a>Taille de disque gérée

Les disques managés sont facturés en fonction des tailles de machine virtuelle choisie lors de la configuration d’une nouvelle machine virtuelle Azure. Azure fait correspondre la taille provisionnée (arrondie à la valeur supérieure) à l’offre de taille de disque la plus proche. Pour plus d’informations sur les tailles de disque proposées, consultez le tableau dans la section Objectifs de performance et de scalabilité ci-dessus. Chaque disque correspond à une taille de disque provisionnée prise en charge et est facturé en conséquence à l’heure. Par exemple, si vous configuré un disque ultra Gio 200 et que vous supprimez au bout de 20 heures, il est mappé à l’offre de taille de disque de 256 Gio et vous serez facturé pour les 256 Gio pour 20 heures. Cette facturation est basée sur la consommation par heure de calcul, indépendamment du volume de données réellement écrites sur le disque.

### <a name="managed-disk-provisioned-iops"></a>Disque géré configuré des e/s

Le nombre d’IOPS est le nombre de demandes que votre application envoie aux disques par seconde. Une opération d’entrée/sortie peut être séquentielle ou aléatoire et consister en lectures ou en écritures. Selon la taille du disque ou le nombre de disques attachés à la machine virtuelle, le nombre moyen d’IOPS est facturé sur une base horaire. Pour plus d’informations sur les nombres d’IOPS de disque proposés, consultez le tableau de la section Cibles de performance et d’évolutivité ci-dessus.

### <a name="managed-disk-provisioned-throughput"></a>Gérés débit approvisionné de disque

Le débit est la quantité de données que votre application envoie aux disques dans un intervalle spécifié, mesuré en octets/seconde. Si votre application effectue de grosses opérations d’entrée/sortie, elle a besoin d’un débit élevé.  

La formule suivante illustre la relation entre le débit et le nombre d’IOPS :  IOPS x taille des E/S = débit

Par conséquent, il est important de déterminer les valeurs optimales de débit et d’E/S par seconde dont a besoin votre application. Lorsque vous essayez d’optimiser une de ces valeurs, l’autre est également affectée. Nous vous recommandons de commencer par un débit correspondant à la taille d’E/S de 16 Kio et de l’ajuster si vous avez besoin de plus de débit.

Pour plus d’informations sur le débit de disque pris en charge sur les disques ultra, consultez le tableau dans la section objectifs de Performance et évolutivité ci-dessus. Tout comme la taille et le nombre d’IOPS du disque, le débit provisionné est facturé à l’heure par nombre de Mo/s provisionnés.

### <a name="ultra-disk-vm-reservation-fee"></a>Frais de réservation de machine virtuelle de disque ultra

Nous vous présentons une fonctionnalité sur la machine virtuelle qui indique votre machine virtuelle est ultra disque compatible. Une machine virtuelle compatible avec les disques Ultra alloue une capacité de bande passante dédiée entre l'instance de machine virtuelle de calcul et l'unité d'échelle de stockage de blocs pour optimiser les performances et réduire la latence. L'ajout de cette fonctionnalité sur la machine virtuelle entraîne des frais de réservation qui ne sont imposés que si vous avez activé la fonctionnalité de disque Ultra sur la machine virtuelle sans y joindre de disque Ultra. Lorsqu’un disque ultra est attaché à la machine virtuelle compatible ultra, ces frais ne s’applique pas. Les frais sont calculés par processeur virtuel provisionné sur la machine virtuelle.

Reportez-vous à la [page de tarification des disques Azure](https://azure.microsoft.com/pricing/details/managed-disks/) pour la nouveau ultra disques tarification disponible en version préliminaire limitée.

### <a name="ultra-disk-preview-scope-and-limitations"></a>Portée de l’aperçu disque ultra et limitations

Dans la version preview, disques ultra :

- Ils sont initialement pris en charge dans la région USA Est 2 dans une seule zone de disponibilité  
- Peut uniquement être utilisé avec des Zones de disponibilité (groupes à haute disponibilité et les déploiements de machine virtuelle unique en dehors de la volonté de Zones n'ont pas la possibilité pour attacher un disque ultra)
- Ils sont pris en charge seulement sur les machines virtuelles ES/DS v3
- Ils sont disponibles seulement comme des disques de données et prennent en charge uniquement une taille de secteur physique de 4 k  
- Ils peuvent être créés seulement comme des disques vides  
- Actuellement, ils peuvent être déployés uniquement à l’aide de modèles Resource Manager, de l’interface CLI et du SDK Python.
- Ils ne prennent pas (encore) en charge les instantanés de disque, les images de machine virtuelle, les groupes à haute disponibilité, les groupes de machines virtuelles identiques et Azure Disk Encryption.
- Ils ne prennent pas (encore) en charge l’intégration à la Sauvegarde Azure ou à Azure Site Recovery.
- Comme pour la  [plupart des préversions](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), cette fonctionnalité ne doit pas être utilisée pour les charges de travail de production avant la mise en disponibilité générale.
