---
title: Fichier include
description: Fichier include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176796"
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