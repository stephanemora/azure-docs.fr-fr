---
title: Créer ou modifier un Peering Exchange à l’aide du Portail Azure
titleSuffix: Azure
description: Créer ou modifier un Peering Exchange à l’aide du Portail Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680937"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Créer ou modifier un Peering Exchange à l’aide du Portail Azure

Cet article explique comment créer un Peering Microsoft Exchange à l’aide du Portail Azure. Il montre également comment vérifier l’état de la ressource, la mettre à jour, la supprimer et la déprovisionner.

Si vous préférez, vous pouvez suivre ce guide en utilisant [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue les [prérequis](prerequisites.md) et la [procédure pas à pas du Peering Exchange](walkthrough-exchange-all.md) avant de commencer la configuration.
* Si vous avez déjà des Peerings Exchange avec Microsoft qui ne sont pas convertis en ressources Azure, consultez [Convertir un Peering Exchange hérité en ressource Azure à l’aide du portail](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Créer et provisionner un Peering Exchange

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Se connecter au portail et sélectionner votre abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Créer un Peering Exchange

Vous pouvez créer une nouvelle demande de Peering à l’aide de la ressource **Peering**.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Lancer la ressource et configurer les paramètres de base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurer les connexions et les soumettre
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Vérifier un Peering Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Modifier un Peering Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Déprovisionner un Peering Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering direct à l’aide du portail](howto-direct-portal.md)
* [Convertir un Peering direct hérité en ressource Azure à l’aide du portail](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez les [FAQ sur le Peering Internet](faqs.md).
