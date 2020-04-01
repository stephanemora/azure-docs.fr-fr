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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773660"
---
La cmdlet PowerShell **Get-AzPeeringLocation** retourne une liste d’emplacements de Peering avec le paramètre obligatoire `Kind`, que vous utiliserez lors d’étapes ultérieures :

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

Voici un exemple qui montre comment utiliser Seattle comme emplacement de Peering pour créer un Peering direct :

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