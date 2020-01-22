---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045098"
---
Cet article utilise des cmdlets PowerShell. Pour exécuter les applets de commande, vous pouvez utiliser Azure Cloud Shell, un environnement d’interpréteur de commandes interactif hébergé dans Azure et utilisé via votre navigateur. Azure Cloud Shell est fourni avec les applets de commande PowerShell Azure préinstallées.

Pour exécuter le code contenu dans cet article sur Azure Cloud Shell, ouvrez une session Cloud Shell, utilisez le bouton **Copier** sur un bloc de code pour copier le code, et collez-le dans la session Cloud Shell avec __Ctrl+Maj+V__ sur Windows et Linux, ou avec __Cmd+Maj+V__ sur macOS. Le texte collé n’est pas exécuté automatiquement : vous devez donc appuyer sur **Entrée** pour l’exécuter.

Vous pouvez lancer Azure Cloud Shell avec :

|  |   |
|-----------------------------------------------|---|
| Sélectionnez **Essayer** dans le coin supérieur droit d’un bloc de code. Cette opération ne copie __pas__ automatiquement le texte dans Cloud Shell. | ![Exemple Essayer pour Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Ouvrez [shell.azure.com](https://shell.azure.com) dans votre navigateur. | [![Bouton Lancer Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Sélectionnez le bouton **Cloud Shell** du menu situé en haut à droite du [portail Azure](https://portal.azure.com). | ![Bouton Cloud Shell du portail Azure](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Exécution de PowerShell en local**

Vous pouvez également installer et exécuter des cmdlets Azure PowerShell en local sur votre ordinateur. Les cmdlets PowerShell sont fréquemment mises à jour. Si vous n’exécutez pas leur dernière version, les valeurs spécifiées dans les instructions peuvent échouer. Pour rechercher les versions d’Azure PowerShell installées sur votre ordinateur, utilisez la cmdlet `Get-Module -ListAvailable Az`. Pour installer ou mettre à jour les cmdlets, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

Si vous utilisez PowerShell localement, assurez-vous de lancer « Connect-AzAccount » pour créer votre connexion à Azure.