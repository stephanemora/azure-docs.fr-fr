---
title: Créer ou modifier un Peering Exchange à l’aide de PowerShell
titleSuffix: Azure
description: Créer ou modifier un Peering Exchange à l’aide de PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 1c0620875e7da4582156080c4a52fd6fbe0e573c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700244"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Créer ou modifier un Peering Exchange à l’aide de PowerShell

Cet article explique comment créer un Peering Exchange Microsoft à l’aide d’applets de commande PowerShell et du modèle de déploiement Resource Manager. Il montre également comment vérifier l’état de la ressource, la mettre à jour, la supprimer et la déprovisionner.

Si vous préférez, vous pouvez suivre ce guide en utilisant le [Portail Azure](howto-exchange-portal.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue les [prérequis](prerequisites.md) et la [procédure pas à pas du Peering Exchange](walkthrough-exchange-all.md) avant de commencer la configuration.
* Si vous avez déjà des Peerings Exchange avec Microsoft qui ne sont pas convertis en ressources Azure, consultez [Convertir un Peering Exchange hérité en ressource Azure à l’aide de PowerShell](howto-legacy-exchange-powershell.md).

### <a name="work-with-azure-powershell"></a>Utilisation d’Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Créer et provisionner un Peering Exchange

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Vous connecter à votre compte Azure et sélectionner votre abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Obtenir la liste des emplacements de Peering compatibles avec le Peering Exchange
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Créer un Peering Exchange
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Obtenir un Peering Exchange
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modifier un Peering Exchange
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Déprovisionner un Peering Exchange

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering direct à l’aide de PowerShell](howto-direct-powershell.md)
* [Convertir un Peering direct hérité en ressource Azure à l’aide de PowerShell](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>Ressources supplémentaires
Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

```powershell
Get-Help Get-AzPeering -detailed
```

Pour plus d’informations, consultez les [FAQ sur le Peering Internet](faqs.md).
