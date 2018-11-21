---
title: Fichier Include
description: Fichier Include
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285777"
---
Cette fonctionnalité est en préversion. Pour l’utiliser, vous devez installer une extension ou un module en préversion.

### <a name="install-extension-for-azure-cli"></a>Installer l'extension pour l'interface de ligne de commande Azure

L'interface de ligne de commande Azure requiert l'[extension Event Grid](/cli/azure/azure-cli-extensions-list).

Dans [CloudShell](/azure/cloud-shell/quickstart) :

* Si vous avez installé l’extension précédemment, mettez-la à jour : `az extension update -n eventgrid`
* Si vous n’avez pas installé l’extension précédemment, faites-le : `az extension add -n eventgrid`

Pour une installation locale :

1. Désinstallez Azure CLI localement.
1. Installez la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.
1. Lancez la fenêtre Commande.
1. Désinstallez les précédentes versions de l’extension `az extension remove -n eventgrid`
1. Installez l’extension `az extension add -n eventgrid`

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
