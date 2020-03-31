---
title: Créer ou modifier un peering direct en utilisant le portail
titleSuffix: Azure
description: Créer ou modifier un peering direct en utilisant le portail
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773980"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Créer ou modifier un peering direct en utilisant le portail

Cet article explique comment créer un Peering direct Microsoft à l’aide du portail. Il montre également comment vérifier l’état de la ressource, la mettre à jour, la supprimer et la déprovisionner.

Si vous préférez, vous pouvez suivre ce guide à l’aide de [PowerShell](howto-direct-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue [Configuration requise](prerequisites.md) et [Procédure pas à pas du Peering direct](walkthrough-direct-all.md) avant de commencer la configuration.
* Si vous avez déjà un Peering direct avec Microsoft, qui n’est pas converti en ressources Azure, consultez [Convertir un Peering direct existant en ressource Azure à l’aide du portail](howto-legacy-direct-portal.md).

## <a name="create-and-provision-a-direct-peering"></a>Créer et provisionner un Peering direct

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Connexion au portail et sélection de votre abonnement
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Créer un Peering direct

Vous pouvez créer une nouvelle demande de Peering à l’aide d’une ressource de **Peering**.

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

* [Créer ou modifier un Peering Exchange à l’aide du portail](howto-exchange-portal.md).
* [Convertir un Peering Exchange hérité en ressource Azure à l’aide du portail](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations, consultez [FAQ sur le peering Internet](faqs.md).
