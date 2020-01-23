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
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773940"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Convertir un peering Exchange existant en ressource Azure en utilisant le portail

Cet article explique comment convertir un peering Exchange hérité existant en ressource Azure à l’aide du portail.

Si vous préférez, vous pouvez suivre ce guide à l’aide de [PowerShell](howto-legacy-exchange-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue [Prérequis](prerequisites.md) et [Procédure pas à pas du peering Exchange](walkthrough-exchange-all.md) avant de commencer la configuration.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Convertir un peering Exchange existant en ressource Azure

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Connexion au portail et sélection de votre abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Convertir le peering Exchange hérité

Vous pouvez convertir des connexions de peering héritées à l’aide d’une ressource de **Peering**.

#### <a name="launch-resource-and-configure-basic-settings"></a>Lancer la ressource et configurer les paramètres de base
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Configurer les connexions et les soumettre
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name=get></a>Vérifier le peering Exchange
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez [FAQ sur le peering Internet](faqs.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un peering Exchange à l’aide du portail](howto-exchange-portal.md)
