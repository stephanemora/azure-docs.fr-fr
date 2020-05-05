---
title: Créer ou modifier un Peering direct à l’aide de PowerShell
titleSuffix: Azure
description: Créer ou modifier un Peering direct à l’aide de PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680779"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Créer ou modifier un Peering direct à l’aide de PowerShell

Cet article explique comment créer un Peering direct Microsoft à l’aide de cmdlets PowerShell et du modèle de déploiement Azure Resource Manager. Il montre également comment vérifier l’état de la ressource, la mettre à jour, la supprimer et la déprovisionner.

Si vous préférez, vous pouvez suivre ce guide en utilisant le [Portail Azure](howto-direct-portal.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue les [prérequis](prerequisites.md) et la [procédure pas à pas du Peering direct](walkthrough-direct-all.md) avant de commencer la configuration.
* Si vous avez déjà des connexions de Peering direct avec Microsoft qui ne sont pas converties en ressources Azure, consultez [Convertir un Peering direct hérité en ressource Azure à l’aide de PowerShell](howto-legacy-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Utiliser Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Créer et provisionner un Peering direct

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Vous connecter à votre compte Azure et sélectionner votre abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>Obtenir la liste des emplacements de Peering compatibles avec le Peering direct
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Créer un Peering direct
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Vérifier un Peering direct
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modifier un Peering direct
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Déprovisionner un Peering direct
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering Exchange à l’aide de PowerShell](howto-exchange-powershell.md)
* [Convertir un Peering Exchange hérité en ressource Azure à l’aide de PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ressources supplémentaires
Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

```powershell
Get-Help Get-AzPeering -detailed
```

Pour plus d’informations, consultez les [FAQ sur le Peering Internet](faqs.md).
