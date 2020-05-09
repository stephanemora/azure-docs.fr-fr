---
title: Convertir un Peering Exchange hérité en ressource Azure à l’aide du Portail Azure
titleSuffix: Azure
description: Convertir un Peering Exchange hérité en ressource Azure à l’aide du Portail Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 87a7a6bca608f1748d3b659eabdc3e941b537377
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678515"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Convertir un Peering Exchange hérité en ressource Azure à l’aide du Portail Azure

Cet article explique comment convertir un Peering Exchange hérité en ressource Azure à l’aide du Portail Azure.

Si vous préférez, vous pouvez suivre ce guide en utilisant [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue les [prérequis](prerequisites.md) et la [procédure pas à pas du Peering Exchange](walkthrough-exchange-all.md) avant de commencer la configuration.

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Convertir un Peering Exchange hérité en ressource Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Se connecter au portail et sélectionner votre abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Convertir le peering Exchange hérité

Vous pouvez convertir des connexions de Peering héritées à l’aide de la ressource **Peering**.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Lancer la ressource et configurer les paramètres de base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurer les connexions et les soumettre
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Vérifier le peering Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez les [FAQ sur le Peering Internet](faqs.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering Exchange à l’aide du portail](howto-exchange-portal.md)
