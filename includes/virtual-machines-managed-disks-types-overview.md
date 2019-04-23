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
ms.openlocfilehash: 6eae536bd19a2c0e5707d8e0b379774b6eb2707a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395582"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quels sont les types de disque disponibles dans Azure ?

Les disques managés Azure proposent actuellement quatre types de disque, trois mis à la disposition générale (GA) et un en préversion. Ces quatre types de disque répondent à des scénarios clients qui leur sont propres.

## <a name="disk-comparison"></a>Comparaison des disques

Le tableau suivant offre une comparaison des niveaux SSD Ultra (préversion), SSD Premium, SSD Standard et HDD pour les disques managés afin de vous aider dans votre choix.

|   | SSD Ultra (préversion)   | SSD Premium   | SSD Standard   | HDD Standard   |
|---------|---------|---------|---------|---------|
|Type de disque   |SSD   |SSD   |SSD   |HDD   |
|Scénario   |Charges de travail gourmandes en E/S, telles que le système SAP HANA, les bases de données de niveau supérieur (par exemple, SQL et Oracle), et autres charges de travail très lourdes en transactions.   |Charges de travail de production et sensibles aux performances   |Serveurs web, applications d’entreprise peu utilisées et Dev/Test   |Sauvegarde, non critique, accès peu fréquent   |
|Taille du disque   |65 536 gibioctet (Gio) (préversion)   |32 767 Gio    |32 767 Gio   |32 767 Gio   |
|Débit max.   |2 000 Mio/s (préversion)   |900 Mio/s   |750 Mio/s   |500 Mio/s   |
|Nb max. d’E/S par seconde   |160 000 (préversion)   |20 000   |6 000 / 750   |2 000   |

## <a name="ultra-ssd-preview"></a>SSD Ultra (préversion)

Les disques Ultra SSD d’Azure (préversion) fournissent un stockage de disque avec un haut débit, un nombre d’IOPS élevé et une faible latence homogène pour les machines virtuelles IaaS Azure. Entre autres avantages, Ultra SSD permet de changer dynamiquement les performances de disque en fonction de vos charges de travail sans avoir à redémarrer les machines virtuelles. Les disques SSD Ultra sont adaptés aux charges de travail qui consomment beaucoup de données comme SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions. Les disques SSD Ultra peuvent uniquement être utilisés en tant que disques de données. Nous vous recommandons d’utiliser des disques SSD Premium comme disques de système d’exploitation.

### <a name="performance"></a>Performances

Lorsque vous approvisionnez un disque Ultra, vous pouvez configurer de manière indépendante la capacité et les performances du disque. Les disques SSD Ultra se déclinent en plusieurs tailles fixes de 4 Gio à 64 Tio, et présentent un modèle de configuration de performances flexible qui vous permet de configurer les IOPS et le débit de manière indépendante.

Voici certaines fonctionnalités clés des disques Ultra SSD :

- Capacité du disque : La capacité des disques SSD Ultra s'échelonne de 4 Gio 64 Tio.
- IOPS du disque : Les disques SSD Ultra prennent en charge des limites d’IOPS de 300 IOPS/Gio, avec un maximum de 160 000 IOPS par disque. Pour atteindre le nombre d’IOPS provisionné, vérifiez que le nombre d’IOPS du disque sélectionné est inférieur au nombre d’IOPS de la machine virtuelle. Le nombre minimal d’IOPS du disque est 100.
- Débit du disque : Avec les disques SSD Ultra, la limite de débit d’un seul disque est de 256 Kio/s pour chaque IOPS approvisionnée, avec un maximum de 2000 Mbits/s par disque (où 1 Mbit/s = 10^6 octets par seconde). Le débit minimal du disque est 1 Mio.

### <a name="disk-size"></a>Taille du disque

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

### <a name="preview-scope-and-limitations"></a>Limitations et étendue de la préversion

Dans la préversion, les disques Ultra SSD présentent les caractéristiques suivantes :

- Ils sont pris en charge dans la région USA Est 2 dans une seule zone de disponibilité  
- Ils peuvent être utilisés seulement avec des zones de disponibilité (les groupes à haute disponibilité et les déploiements de machine virtuelle individuelle en dehors des zones n’ont pas la possibilité d’attacher un disque Ultra)
- Ils sont pris en charge seulement sur les machines virtuelles ES/DS v3
- Ils sont disponibles seulement comme des disques de données et prennent en charge uniquement une taille de secteur physique de 4 k  
- Ils peuvent être créés seulement comme des disques vides  
- Actuellement, ils peuvent être déployés uniquement à l’aide de modèles Azure Resource Manager, de l’interface CLI et du SDK Python.
- Ils ne prennent pas encore en charge les instantanés de disque, les images de machine virtuelle, les groupes à haute disponibilité, les groupes de machines virtuelles identiques et Azure Disk Encryption.
- Ils ne prennent pas encore en charge l’intégration à la Sauvegarde Azure ou à Azure Site Recovery.
- Comme pour la  [plupart des préversions](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), cette fonctionnalité ne doit pas être utilisée pour les charges de travail de production avant la mise en disponibilité générale.
