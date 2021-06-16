---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file, devx-track-azurepowershell
ms.openlocfilehash: 9894787ed29226ad6e8d491b498b082cadd0626b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110690805"
---
 Si vous utilisez Azure Cloud Shell, vous vous connectez à votre compte Azure automatiquement après avoir cliqué sur « Essayer ». Pour vous connecter en local, ouvrez la console PowerShell avec des privilèges élevés et exécutez la cmdlet pour vous connecter.

```azurepowershell
Connect-AzAccount
```

Si vous possédez plusieurs abonnements, procurez-vous la liste de vos abonnements Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Spécifiez l’abonnement à utiliser.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```
