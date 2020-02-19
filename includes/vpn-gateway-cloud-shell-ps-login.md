---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133592"
---
Ouvrez la console PowerShell avec des privilèges élevés.

Si vous exécutez Azure PowerShell en local, connectez-vous à votre compte Azure. L’applet de commande *Connect-AzAccount* vous invite à saisir vos informations d’identification. Une fois que vous êtes identifié, vos paramètres de compte sont téléchargés afin de les mettre à disposition d’Azure PowerShell. Si vous utilisez Azure Cloud Shell, vous n’avez pas besoin d’exécuter *Connect-AzAccount*. Azure Cloud Shell se connecte automatiquement à votre compte Azure.

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