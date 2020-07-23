---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dbca8798af83108ca5e8bdfe5918a25423fd934d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86545089"
---
Azure Managed Disks propose quatre types de disque, chaque type étant destiné à des scénarios clients spécifiques.

## <a name="disk-comparison"></a>Comparaison des disques

Le tableau suivant compare quatre types de disque managé : les disques Ultra, les disques SSD Premium, les disques SSD Standard et les disques HDD Standard pour vous aider dans votre choix.

| Detail | Disque Ultra | SSD Premium | SSD Standard | HDD Standard |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Type de disque   |SSD   |SSD   |SSD   |HDD   |
|Scénario   |Charges de travail gourmandes en E/S, telles que le système [SAP HANA](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md), les bases de données de niveau supérieur (par exemple, SQL et Oracle), et autres charges de travail très lourdes en transactions.   |Charges de travail de production et sensibles aux performances   |Serveurs web, applications d’entreprise peu utilisées et Dev/Test   |Sauvegarde, non critique, accès peu fréquent   |
|Taille maximale du disque   |65 536 gibioctets (Gio)    |32 767 Gio    |32 767 Gio   |32 767 Gio   |
|Débit max.   |2 000 Mo/s    |900 Mo/s   |750 Mo/s   |500 Mo/s   |
|Nb max. d’E/S par seconde   |160 000    |20 000   |6 000 / 750   |2 000   |

## <a name="ultra-disk"></a>Disque Ultra

Les disques Ultra Azure fournissent un stockage sur disque présentant un débit élevé, un nombre d’IOPS élevé et une faible latence homogène pour les machines virtuelles Azure IaaS. Entre autres avantages, les disques Ultra permettent de changer dynamiquement le niveau de performance des disques en fonction de vos charges de travail sans avoir à redémarrer les machines virtuelles. Les disques Ultra sont adaptés aux charges de travail qui consomment beaucoup de données, par exemple SAP HANA, les bases de données de niveau supérieur et les charges de travail avec un grand nombre de transactions. Les disques Ultra peuvent uniquement être utilisés en tant que disques de données. Nous vous recommandons d’utiliser des disques SSD Premium comme disques de système d’exploitation.

### <a name="performance"></a>Performances

Lorsque vous approvisionnez un disque Ultra, vous pouvez configurer de manière indépendante la capacité et les performances du disque. Les disques Ultra se déclinent en plusieurs tailles fixes allant de 4 Gio à 64 Tio. Ils présentent un modèle de configuration de performances flexible qui vous permet de configurer les IOPS et le débit de manière indépendante.

Voici certaines fonctionnalités clés des disques Ultra :

- Capacité du disque : La capacité des disques Ultra va de 4 Gio à 64 Tio.
- IOPS du disque : Les disques Ultra prennent en charge des limites d’IOPS de 300 IOPS/Gio, avec au maximum 160 000 IOPS par disque. Pour atteindre le nombre d’IOPS provisionné, vérifiez que le nombre d’IOPS du disque sélectionné est inférieur à la limite d’IOPS de la machine virtuelle. Le nombre minimal d’IOPS garanti par disque est de 2 IOPS/Gio avec une base de référence globale minimale de 100 IOPS. Par exemple, si vous avez un disque Ultra de 4 Gio, vous avez au minimum 100 IOPS au lieu de huit IOPS.
- Débit du disque : Avec les disques Ultra, la limite de débit d’un seul disque est de 256 Kio/s pour chaque IOPS provisionnée, avec au maximum 2 000 Mbits/s par disque (où 1 Mbits/s = 10^6 octets par seconde). Le débit minimal garanti par disque est de 4 Kio/s pour chaque IOPS provisionnée avec une base de référence globale minimale de 1 Mbits/s.
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

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]
