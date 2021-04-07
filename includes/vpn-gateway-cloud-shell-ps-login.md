---
title: Fichier include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93061607"
---
Si vous exécutez PowerShell en local, ouvrez la console PowerShell avec des privilèges élevés et connectez-vous à votre compte Azure. L’applet de commande *Connect-AzAccount* vous invite à saisir vos informations d’identification. Une fois que vous êtes identifié, vos paramètres de compte sont téléchargés afin de les mettre à disposition d’Azure PowerShell.

Si vous utilisez Azure Cloud Shell au lieu d’exécuter PowerShell en local, vous remarquerez que vous n’avez pas besoin d’exécuter *Connect-AzAccount*. Azure Cloud Shell se connecte automatiquement à votre compte Azure lorsque vous sélectionnez **Essayer**.

1. Si vous exécutez PowerShell en local, connectez-vous.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Si vous possédez plusieurs abonnements, procurez-vous la liste de vos abonnements Azure.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Spécifiez l’abonnement à utiliser.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
