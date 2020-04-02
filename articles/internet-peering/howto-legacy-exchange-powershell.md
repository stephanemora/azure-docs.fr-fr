---
title: Convertir un Peering Exchange hérité en ressource Azure à l’aide de PowerShell
titleSuffix: Azure
description: Convertir un Peering Exchange hérité en ressource Azure à l’aide de PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774000"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Convertir un Peering Exchange hérité en ressource Azure à l’aide de PowerShell

Cet article explique comment convertir un Peering Exchange hérité existant en ressource Azure à l’aide d’applets de commande PowerShell.

Si vous préférez, vous pouvez suivre ce guide à l’aide du [portail](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue [Prérequis](prerequisites.md) et [Procédure pas à pas du Peering Exchange](walkthrough-exchange-all.md) avant de commencer la configuration.

### <a name="working-with-azure-powershell"></a>Utilisation d’Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Convertir un peering Exchange existant en ressource Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Vous connecter à votre compte Azure et sélectionner votre abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Obtenir le Peering Exchange hérité pour la conversion
Vous trouverez ci-dessous un exemple pour obtenir un Peering Exchange hérité à l’emplacement de Peering Seattle :

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

La réponse ressemble à ce qui suit :
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Convertir un Peering hérité
Vous pouvez utiliser la commande ci-dessous pour convertir un Peering Exchange hérité en ressource Azure :

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Notez que lors de la conversion d’un Peering hérité en ressource Azure, les modifications ne sont pas prises en charge.

Voici un exemple de réponse quand le provisionnement de bout en bout a été effectué avec succès :

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Ressources supplémentaires
Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

```powershell
Get-Help Get-AzPeering -detailed
```
Pour plus d’informations, consultez [FAQ sur le Peering Internet](faqs.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering Exchange à l’aide de PowerShell](howto-exchange-powershell.md)
