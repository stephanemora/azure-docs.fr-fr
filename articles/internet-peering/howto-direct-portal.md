---
title: Créer ou modifier un Peering direct à l’aide du Portail Azure
titleSuffix: Azure
description: Créer ou modifier un Peering direct à l’aide du Portail Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681056"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Créer ou modifier un Peering direct à l’aide du Portail Azure

Cet article explique comment créer un Peering direct Microsoft à l’aide du Portail Azure. Il montre également comment vérifier l’état de la ressource, la mettre à jour, la supprimer et la déprovisionner.

Si vous préférez, vous pouvez suivre ce guide en utilisant Azure [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue les [prérequis](prerequisites.md) et la [procédure pas à pas du Peering direct](walkthrough-direct-all.md) avant de commencer la configuration.
* Si vous avez déjà des connexions de Peering direct avec Microsoft qui ne sont pas converties en ressources Azure, consultez [Convertir un Peering direct hérité en ressource Azure à l’aide du portail](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Créer et provisionner un Peering direct

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Se connecter au portail et sélectionner votre abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Créer un Peering direct

Vous pouvez créer une nouvelle demande de Peering à l’aide de la ressource **Peering**.

#### <a name="launch-resource-and-configure-basic-settings"></a>Lancer la ressource et configurer les paramètres de base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurer les connexions et les soumettre
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Vérifier un Peering direct
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Modifier un Peering direct
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Déprovisionner un Peering direct
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering Exchange à l’aide du portail](howto-exchange-portal.md)
* [Convertir un Peering Exchange hérité en ressource Azure à l’aide du portail](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez les [FAQ sur le Peering Internet](faqs.md).
