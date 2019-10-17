---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3dfc3c309fe3583ddd4307cbfe4e55bf6522ffc3
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955860"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quels sont les types de disque disponibles dans Azure ?

Azure Managed Disks propose quatre types de disque, chaque type étant destiné à des scénarios clients spécifiques.

## <a name="disk-comparison"></a>Comparaison des disques

Le tableau suivant compare quatre types de disque managé : les disques Ultra, les disques SSD Premium, les disques SSD Standard et les disques HDD Standard pour vous aider dans votre choix.

|   | Disque Ultra   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Type de disque   |SSD   |SSD   |SSD   |HDD   |
|Scénario   |Charges de travail gourmandes en E/S, telles que le système SAP HANA, les bases de données de niveau supérieur (par exemple, SQL et Oracle), et autres charges de travail très lourdes en transactions.   |Charges de travail de production et sensibles aux performances   |Serveurs web, applications d’entreprise peu utilisées et Dev/Test   |Sauvegarde, non critique, accès peu fréquent   |
|Taille maximale du disque   |65 536 gibioctets (Gio)    |32 767 Gio    |32 767 Gio   |32 767 Gio   |
|Débit max.   |2 000 Mio/s    |900 Mio/s   |750 Mio/s   |500 Mio/s   |
|Nb max. d’E/S par seconde   |160 000    |20 000   |6 000 / 750   |2 000   |

## <a name="ultra-disk"></a>Disque Ultra

Les disques Ultra Azure fournissent un stockage sur disque présentant un débit élevé, un nombre d’IOPS élevé et une faible latence homogène pour les machines virtuelles Azure IaaS. Entre autres avantages, les disques Ultra permettent de changer dynamiquement le niveau de performance des disques en fonction de vos charges de travail sans avoir à redémarrer les machines virtuelles. Les disques Ultra sont adaptés aux charges de travail qui consomment beaucoup de données, par exemple SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions. Les disques Ultra peuvent uniquement être utilisés en tant que disques de données. Nous vous recommandons d’utiliser des disques SSD Premium comme disques de système d’exploitation.

### <a name="performance"></a>Performances

Lorsque vous approvisionnez un disque Ultra, vous pouvez configurer de manière indépendante la capacité et les performances du disque. Les disques Ultra se déclinent en plusieurs tailles fixes allant de 4 Gio à 64 Tio. Ils présentent un modèle de configuration de performances flexible qui vous permet de configurer les IOPS et le débit de manière indépendante.

Voici certaines fonctionnalités clés des disques Ultra :

- Capacité du disque : La capacité des disques Ultra va de 4 Gio à 64 Tio.
- IOPS du disque : Les disques Ultra prennent en charge des limites d’IOPS de 300 IOPS/Gio, avec au maximum 160 000 IOPS par disque. Pour atteindre le nombre d’IOPS provisionné, vérifiez que le nombre d’IOPS du disque sélectionné est inférieur à la limite d’IOPS de la machine virtuelle. Le nombre minimal d’IOPS par disque est de 2 IOPS/Gio, avec une base de référence globale minimale de 100 IOPS. Par exemple, si vous avez un disque Ultra de 4 Gio, vous avez au minimum 100 IOPS au lieu de huit IOPS.
- Débit du disque : Avec les disques Ultra, la limite de débit d’un seul disque est de 256 Kio/s pour chaque IOPS provisionnée, avec au maximum 2 000 Mbits/s par disque (où 1 Mbits/s = 10^6 octets par seconde). Le débit minimal par disque est de 4 Kio/s pour chaque IOPS provisionnée, avec une base de référence globale minimale de 1 Mbits/s.
- Avec les disques Ultra, vous pouvez changer les attributs de performances des disques (IOPS et débit) au moment de l’exécution, sans avoir à détacher ces derniers de la machine virtuelle. Une fois qu’une opération de redimensionnement de performances de disque est émise sur un disque, elle peut mettre jusqu’à une heure à être appliquée. Le nombre d’opérations de redimensionnement de performances pendant une période de 24 heures est limité à quatre. Une opération de redimensionnement de performances peut échouer si la capacité de la bande passante des performances est insuffisante.

### <a name="disk-size"></a>Taille du disque

|Taille de disque (Gio)  |Plafond d’IOPS  |Débit limite (Mbits/s)  |
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

### <a name="ga-scope-and-limitations"></a>Étendue et limitations de la version en disponibilité générale

Pour le moment, les disques Ultra ont des limitations supplémentaires, notamment :

- Ils sont pris en charge dans les régions USA Est 2, Asie Sud-Est et Europe Nord, dans deux zones de disponibilité par région  
- Ils peuvent être utilisés seulement avec des zones de disponibilité (les groupes à haute disponibilité et les déploiements de machine virtuelle individuelle en dehors des zones n’ont pas la possibilité d’attacher un disque Ultra)
- Ils sont pris en charge seulement sur les machines virtuelles ES/DS v3
- Ils sont disponibles seulement comme des disques de données et prennent en charge uniquement une taille de secteur physique de 4 k  
- Ils peuvent être créés seulement comme des disques vides  
- Ils ne prennent pas encore en charge les captures instantanées de disque, les images de machine virtuelle, les groupes à haute disponibilité, les groupes de machines virtuelles identiques et Azure Disk Encryption
- Ils ne prennent pas encore en charge l’intégration aux services Sauvegarde Azure ou Azure Site Recovery
- À l’heure actuelle, le nombre limite d’IOPS sur les machines virtuelles en disponibilité générale est de 80 000.
