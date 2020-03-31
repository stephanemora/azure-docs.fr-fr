---
title: Convertir un peering direct existant en ressource Azure en utilisant le portail
titleSuffix: Azure
description: Convertir un peering direct existant en ressource Azure en utilisant le portail
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773896"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Convertir un peering direct existant en ressource Azure en utilisant le portail

Cet article explique comment convertir un Peering direct hérité existant en ressource Azure à l’aide du portail.

Si vous préférez, vous pouvez suivre ce guide à l’aide de [PowerShell](howto-legacy-direct-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue [Configuration requise](prerequisites.md) et [Procédure pas à pas du Peering direct](walkthrough-direct-all.md) avant de commencer la configuration.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Convertir un Peering direct hérité en ressource Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Connexion au portail et sélection de votre abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Convertir un Peering direct hérité

Vous pouvez convertir des connexions de Peering héritées à l’aide d’une ressource de **Peering**.

#### <a name="launch-resource-and-configure-basic-settings"></a>Lancer la ressource et configurer les paramètres de base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurer les connexions et les soumettre
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Vérifier un Peering direct
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez [FAQ sur le peering Internet](faqs.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering direct à l’aide du portail](howto-direct-portal.md).
