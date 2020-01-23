---
title: Créer ou modifier un Peering Exchange à l’aide du portail
titleSuffix: Azure
description: Créer ou modifier un Peering Exchange à l’aide du portail
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773748"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Créer ou modifier un Peering Exchange à l’aide du portail

Cet article explique comment créer un Peering Microsoft Exchange à l’aide du portail. Il montre également comment vérifier l’état de la ressource, la mettre à jour, la supprimer et la déprovisionner.

Si vous préférez, vous pouvez suivre ce guide à l’aide de [PowerShell](howto-exchange-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue [Prérequis](prerequisites.md) et [Procédure pas à pas du Peering Exchange](walkthrough-exchange-all.md) avant de commencer la configuration.
* Si vous avez déjà des Peerings Exchange avec Microsoft, qui ne sont pas convertis en ressources Azure, consultez [Convertir un Peering Exchange hérité en ressource Azure à l’aide du portail](howto-legacy-exchange-portal.md).

## <a name="create-and-provision-an-exchange-peering"></a>Créer et provisionner un Peering Exchange

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Connexion au portail et sélection de votre abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Créer un Peering Exchange

Vous pouvez créer une nouvelle demande de Peering à l’aide d’une ressource de **Peering**.

#### <a name="launch-resource-and-configure-basic-settings"></a>Lancer la ressource et configurer les paramètres de base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurer les connexions et les soumettre
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>Vérifier un Peering Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>Modifier un Peering Exchange
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>Annuler le provisionnement d’un Peering Exchange
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering direct à l’aide du portail](howto-direct-portal.md)
* [Convertir un Peering direct hérité en ressource Azure à l’aide du portail](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez [FAQ sur le Peering Internet](faqs.md).
