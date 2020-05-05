---
title: Convertir un Peering direct hérité en ressource Azure à l’aide du portail Azure
titleSuffix: Azure
description: Convertir un Peering direct hérité en ressource Azure à l’aide du portail Azure
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678854"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Convertir un Peering direct hérité en ressource Azure à l’aide du portail Azure

Cet article explique comment convertir un Peering direct hérité en ressource Azure à l’aide du Portail Azure.

Si vous préférez, vous pouvez suivre ce guide en utilisant [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue les [prérequis](prerequisites.md) et la [procédure pas à pas du Peering direct](walkthrough-direct-all.md) avant de commencer la configuration.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Convertir un Peering direct hérité en ressource Azure

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Se connecter au portail et sélectionner votre abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Convertir un Peering direct hérité

Vous pouvez convertir des connexions de Peering héritées à l’aide de la ressource **Peering**.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Lancer la ressource et configurer les paramètres de base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurer les connexions et les soumettre
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Vérifier un Peering Direct
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez les [FAQ sur le Peering Internet](faqs.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering direct à l’aide du portail](howto-direct-portal.md)
