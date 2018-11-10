---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b878d54f0f52768459dbfc810e47d294b9c8d996
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097764"
---
Cet article utilise des applets de commande PowerShell. Pour exécuter les applets de commande, vous pouvez utiliser Azure Cloud Shell, un interpréteur de commandes interactif gratuit. Il contient des outils Azure courants préinstallés et configurés pour être utilisés avec votre compte. Cliquez simplement sur **Copier** pour copier le code, collez-le dans Cloud Shell, et appuyez sur Entrée pour l’exécuter. Cloud Shell peut être lancé de plusieurs façons :

|  |   |
|-----------------------------------------------|---|
| Cliquez sur **Essayer** dans le coin supérieur droit d’un bloc de code. | ![Cloud Shell dans cet article](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Ouvrez Cloud Shell dans votre navigateur. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Cliquez sur le bouton **Cloud Shell** du menu situé dans l’angle supérieur droit de la fenêtre du portail Azure. | [![Cloud Shell dans le portail](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Si vous ne souhaitez pas utiliser Azure Cloud Shell, vous pouvez installer PowerShell localement. Si vous choisissez d’installer et d’utiliser PowerShell localement, veillez à installer la dernière version des applets de commande PowerShell Azure Resource Manager. Les applets de commande PowerShell sont fréquemment mises à jour, et vous devez généralement mettre à jour les vôtres pour obtenir les toutes dernières fonctionnalités. Si vous ne mettez pas à jour vos cmdlets PowerShell, les valeurs spécifiées peuvent échouer. Pour déterminer la version de PowerShell que vous exécutez localement, utilisez l’applet de commande « Get-Module - ListAvailable AzureRM ». Si vous devez effectuer une mise à niveau, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/install-azurerm-ps). Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/overview).