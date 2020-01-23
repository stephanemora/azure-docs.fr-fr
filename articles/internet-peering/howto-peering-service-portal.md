---
title: Activer Peering Service sur un Peering direct à l’aide du portail
titleSuffix: Azure
description: Activer Peering Service sur un Peering direct à l’aide du portail
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75773872"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Activer Peering Service sur un Peering direct à l’aide du portail

Cet article explique comment activer [Peering Service](overview-peering-service.md) sur un Peering direct en utilisant le portail.

Si vous préférez, vous pouvez suivre ce guide à l’aide de [PowerShell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue les [prérequis](prerequisites.md) avant de commencer la configuration.
* Choisissez un Peering direct dans votre abonnement sur lequel vous souhaitez activer Peering Service. Si vous n’en avez pas, vous pouvez soit convertir un Peering direct hérité, soit créer un nouveau Peering direct.
    * Pour convertir un Peering direct hérité, suivez les instructions fournies dans [Convertir un Peering direct existant en ressource Azure à l’aide du portail](howto-legacy-direct-portal.md).
    * Pour créer un Peering direct, suivez les instructions fournies dans [Créer ou modifier un Peering direct à l’aide du portail](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Activer Peering Service sur un Peering direct

### <a name= get></a>Afficher un Peering direct
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>Activer le Peering direct pour Peering Service

Après avoir ouvert le Peering direct à l’étape précédente, activez-le pour Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Modifier une connexion de Peering direct

Si vous avez besoin de modifier les paramètres de connexion, consultez la section **Modifier un Peering direct** dans [Créer ou modifier un Peering direct à l’aide du portail](howto-direct-portal.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering Exchange à l’aide du portail](howto-exchange-portal.md)
* [Convertir un Peering Exchange hérité en ressource Azure à l’aide du portail](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour obtenir des réponses aux questions les plus fréquentes, consultez [FAQ sur Peering Service](service-faqs.md).