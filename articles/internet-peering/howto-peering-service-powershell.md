---
title: Activer Peering Service sur un Peering direct à l’aide de PowerShell
titleSuffix: Azure
description: Activer Peering Service sur un Peering direct à l’aide de PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773628"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Activer Peering Service sur un Peering direct à l’aide de PowerShell

Cet article explique comment activer [Peering Service](overview-peering-service.md) sur un Peering direct à l’aide d’applets de commande PowerShell et du modèle de déploiement Resource Manager.

Si vous préférez, vous pouvez suivre ce guide à l’aide du [portail](howto-peering-service-portal.md).

## <a name="before-you-begin"></a>Avant de commencer
* Passez en revue les [prérequis](prerequisites.md) avant de commencer la configuration.
* Choisissez un Peering direct dans votre abonnement sur lequel vous souhaitez activer Peering Service. Si vous n’en avez pas, vous pouvez soit convertir un Peering direct hérité, soit créer un nouveau Peering direct.
    * Pour convertir un Peering direct hérité, suivez les instructions fournies dans [Convertir un Peering direct existant en ressource Azure à l’aide de PowerShell](howto-legacy-direct-powershell.md).
    * Pour créer un Peering direct, suivez les instructions fournies dans [Créer ou modifier un Peering direct à l’aide de PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Utilisation d’Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Activer Peering Service sur un peering direct

### <a name="view-direct-peering"></a><a name= get></a>Afficher un Peering direct
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Activer le Peering direct pour Peering Service

Après avoir obtenu le Peering direct à l’étape précédente, activez-le pour Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Modifier une connexion de Peering direct

Si vous avez besoin de modifier les paramètres de connexion, consultez la section **Modifier un Peering direct** dans [Créer ou modifier un Peering direct à l’aide de PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Étapes suivantes

* [Créer ou modifier un Peering Exchange à l’aide de PowerShell](howto-exchange-powershell.md)
* [Convertir un Peering Exchange hérité en ressource Azure à l’aide de PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ressources supplémentaires
Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

```powershell
Get-Help Get-AzPeering -detailed
```

Pour obtenir des réponses aux questions les plus fréquentes, consultez [FAQ sur Peering Service](service-faqs.md).