---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201480"
---
Ouvrez la console PowerShell avec des privilèges élevés.



Si vous exécutez Azure PowerShell en local, connectez-vous à votre compte Azure. L’applet de commande *Connect-AzureRmAccount* vous invite à saisir vos informations d’identification. Une fois que vous êtes identifié, vos paramètres de compte sont téléchargés afin de les mettre à disposition d’Azure PowerShell. Si vous n’exécutez pas PowerShell localement mais que vous utilisez la fonctionnalité Azure Cloud Shell « Essayer » dans le navigateur, passez cette première étape. Vous serez automatiquement connecté à votre compte Azure.

```azurepowershell
Connect-AzureRmAccount
```

Si vous possédez plusieurs abonnements, procurez-vous la liste de vos abonnements Azure.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Spécifiez l’abonnement à utiliser.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```