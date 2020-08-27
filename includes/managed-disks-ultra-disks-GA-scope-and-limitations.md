---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 546c21802d275fe99ed2acbf00e32d37db3603f2
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225192"
---
Pour le moment, les disques Ultra ont des limitations supplémentaires, notamment :

Les seules options de redondance d’infrastructure disponibles pour les disques Ultra sont les zones de disponibilité. Les machines virtuelles utilisant d’autres options de redondance ne peuvent pas attacher un disque Ultra.

Le tableau suivant présente les régions où les disques Ultra sont disponibles ainsi que les options de disponibilité correspondantes :

> [!NOTE]
> Si l’une des régions de la liste suivante ne dispose pas de zones de disponibilité compatibles avec un disque Ultra, les machines virtuelles de cette région doivent être déployées sans option de redondance d’infrastructure pour pouvoir attacher un disque Ultra.

|Régions  |Nombre de zones de disponibilité prenant en charge les disques Ultra  |
|---------|---------|
|Gouvernement américain - Virginie     |None         |
|Gouvernement des États-Unis – Arizona     |None         |
|États-Unis - partie centrale méridionale     |None         |
|USA Centre     |Trois zones         |
|USA Ouest     |None         |
|USA Ouest 2    |Trois zones         |
|USA Est     |Trois zones         |
|USA Est 2     |Trois zones         |
|Asie Sud-Est     |Trois zones         |
|Asie Est     |Aucun         |
|Europe Nord     |Trois zones          |
|Europe Ouest     |Trois zones          |
|Sud du Royaume-Uni     |Trois zones          |
|Japon Est     |Trois zones         |
|France Centre    |Deux zones        |
|Brésil Sud    |Aucun        |
|Australie Est    |Trois zones        |
|Canada Centre*    |Trois zones        |

\* Contactez Support Azure pour obtenir l’accès à la fonctionnalité Zones de disponibilité pour cette région.

- Ils ne sont pris en charge que sur les séries de machines virtuelles suivantes :
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series)
    - [Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series)
    - [Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Les tailles de machines virtuelles ne sont pas toutes disponibles dans toutes les régions prises en charge avec des disques Ultra
- Ils sont disponibles seulement comme des disques de données et prennent en charge uniquement une taille de secteur physique de 4 K. En raison de la taille native de secteur de 4 K des disques Ultra, certaines applications ne sont pas compatibles avec ces derniers. Citons à titre d’exemple Oracle Database, qui nécessite la version 12.2 ou une version ultérieure afin de prendre en charge les disques Ultra.  
- Ils peuvent être créés seulement comme des disques vides  
- Ils ne prennent pas en charge les instantanés de disque, les images de machine virtuelle, les groupes à haute disponibilité, les hôtes dédiés Azure ou Azure Disk Encryption
- Ils ne prennent pas en charge l’intégration aux services Sauvegarde Azure ou Azure Site Recovery
- Prend uniquement en charge les lectures non mises en cache et les écritures non mises en cache
- À l’heure actuelle, le nombre limite d’IOPS sur les machines virtuelles en disponibilité générale est de 80 000.

Les disques Ultra Azure offrent jusqu’à 16 Tio par région et par abonnement par défaut, mais ils prennent en charge une plus grande capacité par demande. Pour demander une augmentation de la capacité, contactez le Support Azure.