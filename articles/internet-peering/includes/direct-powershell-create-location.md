---
title: Fichier include
titleSuffix: Azure
description: Fichier include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680759"
---
La cmdlet PowerShell **Get-AzPeeringLocation** retourne une liste d’emplacements de Peering avec le paramètre obligatoire `Kind`, que vous utiliserez dans des étapes ultérieures.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Les emplacements de Peering direct contiennent les champs suivants :
* PeeringLocation 
* Country
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Vérifiez que vous vous trouvez à l’installation de Peering souhaitée en vous reportant à [PeeringDB](https://w www.peeringdb.com).

Cet exemple montre comment utiliser Seattle comme emplacement de Peering pour créer un Peering direct.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```