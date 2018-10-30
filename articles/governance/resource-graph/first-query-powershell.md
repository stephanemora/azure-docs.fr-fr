---
title: Exécuter votre première requête Resource Graph à l’aide d’Azure PowerShell
description: Cet article vous guide tout au long des étapes à suivre pour activer le module Resource Graph pour Azure PowerShell et exécuter votre première requête.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 001805aaf87ed6c3481a8ad8378cdc22ef74d274
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646386"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Exécuter votre première requête Resource Graph à l’aide d’Azure PowerShell

La première étape est de vérifier que le module Azure Resource Graph pour Azure PowerShell est installé. Ce guide de démarrage rapide décrit le processus d’ajout du module à votre installation Azure PowerShell.

Au terme de ce processus, vous aurez ajouté le module à l’installation Azure PowerShell de votre choix et vous pourrez exécuter votre première requête Resource Graph.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="add-the-resource-graph-module"></a>Ajouter le module Resource Graph

Pour permettre à Azure PowerShell d’interroger Azure Resource Graph, vous devez ajouter le module. Ce module peut être utilisé avec les applications Windows PowerShell et PowerShell Core installées localement, ou avec l’[image Azure PowerShell Docker](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Configuration de base requise

Le module Azure Resource Graph nécessite les logiciels suivants :

- Azure PowerShell 6.3.0 ou version ultérieure. S’il n’est pas installé, suivez [ces instructions](/powershell/azure/install-azurerm-ps).

  - Pour PowerShell Core, utilisez la version **Az** du module Azure PowerShell.

  - Pour Windows PowerShell, utilisez la version **AzureRm** du module Azure PowerShell.

  > [!NOTE]
  > Il n’est actuellement pas recommandé d’installer le module dans Cloud Shell.

- PowerShellGet 2.0.1 ou une version ultérieure. S’il n’est pas installé ou mis à jour, suivez [ces instructions](/powershell/gallery/installing-psget).

### <a name="cloud-shell"></a>Cloud Shell

Pour ajouter le module Azure Resource Graph dans Cloud Shell, suivez les instructions ci-dessous pour PowerShell Core.

### <a name="powershell-core"></a>PowerShell Core

Le module Resource Graph pour PowerShell Core est **Az.ResourceGraph**.

1. À partir d’une invite PowerShell Core **d’administration**, exécutez la commande suivante :

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Vérifiez que le module a été importé et que sa version est correcte (0.3.0) :

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Activez les alias inversés **Az** sur **AzureRm** avec la commande suivante :

   ```azurepowershell-interactive
   # Enable backwards alias compatibility
   Enable-AzureRmAlias
   ```

### <a name="windows-powershell"></a>Windows PowerShell

Le module Resource Graph pour Windows PowerShell est **AzureRm.ResourceGraph**.

1. À partir d’une invite Windows PowerShell **d’administration**, exécutez la commande suivante :

   ```powershell
   # Install the Resource Graph (prerelease) module from PowerShell Gallery
   Install-Module -Name AzureRm.ResourceGraph -AllowPrerelease
   ```

1. Vérifiez que le module a été importé et que sa version est correcte (0.1.1-preview) :

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Exécuter votre première requête Resource Graph

Une fois le module Azure PowerShell ajouté à l’environnement de votre choix, vous pouvez exécuter une requête Resource Graph simple. La requête retourne les cinq premières ressources Azure avec le nom (**name**) et le **type** de chaque ressource.

1. Exécutez votre première requête Azure Resource Graph à l’aide de l’applet de commande `Search-AzureRmGraph` :

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Comme cet exemple de requête ne fournit pas un modificateur de tri tel que `order by`, l’exécution répétée de cette requête peut produire un ensemble différent de ressources.

1. Mettez à jour la requête pour la trier (`order by`) en fonction du nom (**name**) de la propriété :

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Comme précédemment, l’exécution répétée de cette requête peut produire un ensemble différent de ressources. L’ordre des commandes de requête est important. Dans cet exemple, `order by` vient après `limit`. Cela signifie que les résultats de la requête sont d’abord limités avant d’être triés.

1. Mettez à jour la requête pour d’abord trier (`order by`) les résultats en fonction de la propriété **name**, puis les limiter (`limit`) aux cinq premiers :

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Si votre environnement ne change pas et que vous exécutez plusieurs fois la requête finale, les résultats retournés sont cohérents et conformes aux attentes. En effet, ils sont classés en fonction de la propriété **name** et limités aux cinq premiers.

## <a name="cleanup"></a>Nettoyage

Si vous souhaitez supprimer le module Resource Graph de votre environnement Azure PowerShell, vous pouvez le faire à l’aide de la commande suivante :

```powershell
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> Cette opération ne supprime pas le fichier du module téléchargé précédemment. Elle le supprime uniquement de la session PowerShell en cours d’exécution.

## <a name="next-steps"></a>Étapes suivantes

- Obtenir plus d’informations sur le [langage de requête](./concepts/query-language.md)
- Apprendre à [explorer les ressources](./concepts/explore-resources.md)
- Exécuter votre première requête avec [Azure CLI](first-query-azurecli.md)
- Consulter des exemples de [requêtes de démarrage](./samples/starter.md)
- Consulter des exemples de [requêtes avancées](./samples/advanced.md)
- Envoyer des commentaires sur [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)