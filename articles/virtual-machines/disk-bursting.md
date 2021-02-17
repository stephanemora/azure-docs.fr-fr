---
title: Mode rafale des disques managés
description: Découvrez le bursting de disque pour les disques Azure et les machines virtuelles Azure.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576191"
---
# <a name="managed-disk-bursting"></a>Mode rafale des disques managés
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Mode rafale au niveau des machines virtuelles
La fonctionnalité de bursting au niveau machine virtuelle est activée sur la série de machines virtuelles suivante dans toutes les régions dans lesquelles elles sont prises en charge :
- [Série Lsv2](lsv2-series.md)
- [Dsv3-series](dv3-dsv3-series.md)
- [Série Esv3](ev3-esv3-series.md)

Le mode rafale est activé par défaut pour les machines virtuelles qui le prennent en charge.

## <a name="disk-level-bursting"></a>Mode rafale au niveau des disques
Le mode rafale est également disponible sur nos [SSD Premium](disks-types.md#premium-ssd) pour les tailles de disque P20 et inférieures dans toutes les régions des clouds Azure publics, gouvernementaux et en Chine. Le mode rafale du disque est activé par défaut sur tous les déploiements nouveaux et existants des tailles de disque qui le prennent en charge. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]
