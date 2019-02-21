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
ms.openlocfilehash: 188f70d17531a3a7da12444ce5f1c97385ea33bc
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418039"
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
