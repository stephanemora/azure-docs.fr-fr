---
title: Fichier Include
titleSuffix: Azure
description: Fichier Include
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
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

Vérifiez que vous vous trouvez à l’installation de Peering souhaitée en vous reportant à [PeeringDB](https://w www.peeringdb.com).

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