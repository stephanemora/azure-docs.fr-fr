---
title: Mode rafale des disques managés
description: Découvrez le mode rafale des disques pour les disques Azure et le mode rafale des disques pour les machines virtuelles Azure
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab54b68ab3f7984ee18a39cf3a81fa663af54dee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889109"
---
# <a name="disk-bursting"></a>Mode rafale des disques
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Mode rafale au niveau des machines virtuelles
La prise en charge du mode rafale au niveau des machines virtuelles est activée dans toutes les régions du cloud public sur les tailles prises en charge suivantes : 
- [Série Lsv2](../lsv2-series.md)

Le mode rafale au niveau des machines virtuelles est également disponible dans USA Centre-Ouest pour les tailles prises en charge suivantes :
- [Dsv3-series](../dv3-dsv3-series.md)
- [Série Esv3](../ev3-esv3-series.md)

Le mode rafale est activé par défaut pour les machines virtuelles qui le prennent en charge.

## <a name="disk-level-bursting"></a>Mode rafale au niveau des disques
Le mode rafale est également disponible sur nos [disques SSD Premium](disks-types.md#premium-ssd) pour les tailles de disque P20 et inférieures dans toutes les régions. Le mode rafale du disque est activé par défaut sur les nouveaux déploiements des tailles de disque qui le prennent en charge. Les tailles de disque existantes, si elles prennent en charge le mode rafale du disque, peuvent activer les rafales via l’une des méthodes suivantes : 
- **Redémarrer la machine virtuelle** 
- **Détacher et rattacher le disque**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
