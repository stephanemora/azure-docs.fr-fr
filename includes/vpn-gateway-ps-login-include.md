---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405842"
---
Avant de commencer cette configuration, vous devez vous connecter à votre compte Azure. Les cmdlets vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, la cmdlet télécharge vos paramètres de compte pour qu’ils soient reconnus par Azure PowerShell. Pour plus d’informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../articles/powershell-azure-resource-manager.md).

Pour vous connecter, ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

```powershell
Connect-AzAccount
```

Si vous disposez de plusieurs abonnements Azure, vérifiez les abonnements associés au compte.

```powershell
Get-AzSubscription
```

Spécifiez l’abonnement à utiliser.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
