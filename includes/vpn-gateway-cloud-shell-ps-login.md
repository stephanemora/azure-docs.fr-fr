---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67176926"
---
Ouvrez la console PowerShell avec des privilèges élevés.



Si vous exécutez Azure PowerShell en local, connectez-vous à votre compte Azure. L’applet de commande *Connect-AzAccount* vous invite à saisir vos informations d’identification. Une fois que vous êtes identifié, vos paramètres de compte sont téléchargés afin de les mettre à disposition d’Azure PowerShell. Si vous n’exécutez pas PowerShell localement mais que vous utilisez la fonctionnalité Azure Cloud Shell « Essayer » dans le navigateur, passez cette première étape. Vous serez automatiquement connecté à votre compte Azure.

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