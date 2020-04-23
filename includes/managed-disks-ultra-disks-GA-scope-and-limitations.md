---
title: Fichier include
description: Fichier include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008630"
---
Pour le moment, les disques Ultra ont des limitations supplémentaires, notamment :

Les seules options de redondance d’infrastructure disponibles pour les disques Ultra sont les zones de disponibilité. Les machines virtuelles utilisant d’autres options de redondance ne peuvent pas attacher un disque Ultra.

Le tableau suivant présente les régions où les disques Ultra sont disponibles ainsi que les options de disponibilité correspondantes :

> [!NOTE]
> Certaines zones de disponibilité dans ces régions n’offrent pas de disques Ultra.

|Régions  |Aucune redondance de l’infrastructure  |Zones de disponibilité  |
|---------|---------|---------|
|USA Ouest     |Oui         |Non         |
|USA Ouest 2    |Non         |Oui         |
|USA Est     |Non         |Oui         |
|USA Est 2     |Non         |Oui         |
|Asie Sud-Est     |Non         |Oui         |
|Europe Nord     |Non         |Oui         |
|Europe Ouest     |Non         |Oui         |
|Sud du Royaume-Uni     |Non         |Oui         |

- Ils ne sont pris en charge que sur les séries de machines virtuelles suivantes :
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- Les tailles de machines virtuelles ne sont pas toutes disponibles dans toutes les régions prises en charge avec des disques Ultra
- Ils sont disponibles seulement comme des disques de données et prennent en charge uniquement une taille de secteur physique de 4 K. En raison de la taille native de secteur de 4 K des disques Ultra, certaines applications ne sont pas compatibles avec ces derniers. Citons à titre d’exemple Oracle Database, qui nécessite la version 12.2 ou une version ultérieure afin de prendre en charge les disques Ultra.  
- Ils peuvent être créés seulement comme des disques vides  
- Ils ne prennent pas en charge les instantanés de disque, les images de machine virtuelle, les groupes à haute disponibilité, les hôtes dédiés Azure ou Azure Disk Encryption
- Ils ne prennent pas en charge l’intégration aux services Sauvegarde Azure ou Azure Site Recovery
- À l’heure actuelle, le nombre limite d’IOPS sur les machines virtuelles en disponibilité générale est de 80 000.

Les disques Ultra Azure offrent jusqu’à 16 Tio par région et par abonnement par défaut, mais ils prennent en charge une plus grande capacité par demande. Pour demander une augmentation de la capacité, contactez le Support Azure.