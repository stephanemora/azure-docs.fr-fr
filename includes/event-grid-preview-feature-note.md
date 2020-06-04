---
title: Fichier include
description: Fichier include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814812"
---
Cette fonctionnalité est en préversion. Pour l’utiliser, vous devez installer une extension ou un module en préversion.

### <a name="install-extension-for-azure-cli"></a>Installer l'extension pour l'interface de ligne de commande Azure

L'interface de ligne de commande Azure requiert l'[extension Event Grid](/cli/azure/azure-cli-extensions-list).

Dans [CloudShell](/azure/cloud-shell/quickstart) :

* Si vous avez installé l’extension précédemment, mettez-la à jour : `az extension update -n eventgrid`
* Si vous n’avez pas installé l’extension précédemment, faites-le : `az extension add -n eventgrid`

Pour une installation locale :

1. [Installer l’interface de ligne de commande Microsoft Azure](/cli/azure/install-azure-cli). Vérifiez que la dernière version est installée en exécutant `az --version`.
1. Désinstallez les précédentes versions de l’extension `az extension remove -n eventgrid`
1. Installez l’extension `eventgrid` avec `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Installez le module pour PowerShell

PowerShell requiert le [module AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

Dans [CloudShell](/azure/cloud-shell/quickstart-powershell) :

* Installez le module `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Pour une installation locale :

1. Ouvrez la console PowerShell en tant qu’administrateur
1. Installez le module `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Si le paramètre `-AllowPrerelease` n’est pas disponible, procédez comme suit :

1. Exécutez `Install-Module PowerShellGet -Force`
1. Exécutez `Update-Module PowerShellGet`
1. Fermez la console PowerShell
1. Redémarrez PowerShell en tant qu’administrateur
1. Installez le module `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
