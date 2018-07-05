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
ms.openlocfilehash: d4d370e6b76fcfc502366642842bfeb923a13991
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313674"
---
Avant de commencer cette configuration, vous devez vous connecter à votre compte Azure. Les cmdlets vous invitent à entrer les informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, la cmdlet télécharge vos paramètres de compte pour qu’ils soient reconnus par Azure PowerShell. Pour plus d’informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../articles/powershell-azure-resource-manager.md).

Pour vous connecter, ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

```powershell
Connect-AzureRmAccount
```

Si vous disposez de plusieurs abonnements Azure, vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

Spécifiez l’abonnement à utiliser.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
