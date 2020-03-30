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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773652"
---
Cette section décrit comment effectuer les opérations de modification suivantes pour le peering direct :

* Ajouter les connexions de Peering direct
* Supprimer les connexions de Peering direct
* Mettez à niveau ou rétrogradez la bande passante sur les connexions actives.
* Ajoutez une session IPv4/IPv6 sur les connexions actives.
* Supprimez la session IPv4/IPv6 sur les connexions actives.

### <a name="add-direct-peering-connections"></a>Ajouter les connexions de Peering direct

L’exemple ci-dessous décrit comment ajouter des connexions au Peering direct existant.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>Supprimer les connexions de Peering direct

Pour le moment, PowerShell ne prend pas en charge la suppression d’une connexion. Contactez le [Peering Microsoft](mailto:peeringexperience@microsoft.com).

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Mettre à niveau ou rétrograder la bande passante sur les connexions actives

L’exemple ci-dessous décrit comment ajouter 10 Gbits/s à une connexion directe existante.

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Ajoutez une session IPv4/IPv6 sur les connexions actives.

L’exemple ci-dessous décrit comment ajouter une session IPv6 sur une connexion directe existante avec une seule session IPv4. 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Supprimez la session IPv4/IPv6 sur les connexions actives.

La suppression d’une session IPv4/IPv6 à partir d’une connexion existante n’est actuellement pas prise en charge sur PowerShell. Contactez le [Peering Microsoft](mailto:peeringexperience@microsoft.com).