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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65f282a63e95530549bd0db35c2f42fe5c25993d
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110720883"
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

Vérifiez que vous vous trouvez à l’installation de Peering souhaitée en vous reportant à [PeeringDB](https://wwww.peeringdb.com).

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
