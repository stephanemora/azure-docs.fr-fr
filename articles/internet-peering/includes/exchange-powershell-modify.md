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
ms.openlocfilehash: 86e1a9cce1864ce259fe07b6949be2e32be242a8
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773700"
---
L’opération de modification suivante est prise en charge pour le peering Exchange
1. Ajouter des connexions de peering Exchange
1. Supprimer des connexions de peering Exchange
1. Ajoutez une session IPv4/IPv6 sur les connexions actives.
1. Supprimez la session IPv4/IPv6 sur les connexions actives.


### <a name="add-exchange-peering-connections"></a>Ajouter des connexions de peering Exchange

L’exemple ci-dessous décrit comment ajouter des connexions au peering Exchange existant.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>Supprimer des connexions de peering Exchange

L’exemple ci-dessous décrit comment supprimer des connexions au peering Exchange existant.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

Affichez toutes les connexions et sélectionnez la connexion que vous souhaitez supprimer. 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

Dans la commande ci-dessous, au lieu de 0, entrez le numéro d’index de la connexion que vous souhaitez supprimer.

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>Ajouter une session IPv4/IPv6 sur les connexions actives

L’exemple ci-dessous décrit comment ajouter une session IPv6 à une connexion Exchange existante.

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>Supprimer la session IPv4/IPv6 sur les connexions actives

La suppression d’une session IPv4/IPv6 à partir d’une connexion existante n’est actuellement pas prise en charge sur PowerShell. Contactez le [Peering Microsoft](mailto:peeringexperience@microsoft.com).