---
title: Effectuer des opérations sur Stockage Table Azure avec PowerShell | Microsoft Docs
description: Effectuez des opérations sur le Stockage Table Azure avec PowerShell.
services: cosmos-db
author: roygara
ms.service: cosmos-db
ms.topic: article
ms.date: 03/27/2019
ms.author: rogarana
ms.subservice: cosmosdb-table
ms.openlocfilehash: bb8f0fd98296d0cc4de1596480988b154a731d41
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58540225"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Effectuer des opérations sur Stockage Table Azure avec Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Le stockage Table Azure est une banque de données NoSQL que vous pouvez utiliser pour stocker et interroger de grands ensembles de données non relationnelles et structurées. Les principaux composants du service sont les tables, les entités et les propriétés. une table est une collection d’entités. Une entité est un ensemble de propriétés. Chaque entité peut avoir jusqu’à 252 propriétés, qui sont toutes des paires nom-valeur. Cet article suppose que vous êtes déjà familiarisé avec les concepts du service Stockage Table Azure. Pour plus d’informations, consultez [Présentation du modèle de données du service de Table](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) et [Prise en main d’Azure Table Storage à l’aide de .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

Cet article sur les procédures décrit les opérations courantes liées à Stockage Table Azure. Vous allez apprendre à effectuer les actions suivantes : 

> [!div class="checklist"]
> * Création d’une table
> * Récupération d’une table
> * Ajout d’entités de table
> * Interrogation d’une table
> * Suppression d’entités de table
> * Suppression d’une table

Cet article sur les procédures vous montre comment créer un nouveau compte de stockage Azure dans un nouveau groupe de ressources afin que vous puissiez le supprimer facilement quand vous avez terminé. Si vous préférez, vous pouvez utiliser un compte de stockage existant à la place.

Les exemples requièrent des modules PowerShell de Az `Az.Storage (1.1.3 or greater)` et `Az.Resources (1.2.0 or greater)`. Dans une fenêtre PowerShell, exécutez `Get-Module -ListAvailable Az*` pour trouver la version. Si aucune information n’est affichée ou que vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Pour utiliser cette fonctionnalité Azure à partir de PowerShell, vous devez installer le module `Az`. La version actuelle de AzureRmStorageTable n’est pas compatible avec le module AzureRM plus anciens.
> Suivez le [plus tard instructions d’installation pour installer le module de Az](/powershell/azure/install-az-ps) si nécessaire.

Une fois Azure PowerShell installé ou mis à jour, vous devez installer le module **AzureRmStorageTable**, qui contient les commandes de gestion des entités. Pour installer ce module, exécutez PowerShell en tant qu’administrateur et utilisez la commande **Install-Module**.

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Add-AzAccount` et suivez les instructions à l’écran.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Récupérer la liste des régions

Si vous ne savez pas quelle région utiliser, listez celles qui sont disponibles. Dans la liste, trouvez celle que vous souhaitez utiliser. Ces exemples utilisent **eastus**. Stockez cette valeur dans la variable **location** pour une utilisation ultérieure.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Stockez le nom du groupe de ressources dans une variable pour une utilisation ultérieure. Dans cet exemple, un groupe de ressources nommé *pshtablesrg* est créé dans la région *eastus*.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Créer un compte de stockage

Créez un compte de stockage universel standard avec un stockage localement redondant (LRS) à l’aide de [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Veillez à spécifier un nom de compte de stockage unique. Ensuite, obtenez le contexte qui représente le compte de stockage. Lorsqu’il agit sur un compte de stockage, vous pouvez référencer le contexte au lieu de fournir plusieurs fois vos informations d’identification.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Créer une table

Pour créer une table, utilisez le [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) applet de commande. Dans cet exemple, la table est appelée `pshtesttable`.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Récupérer une liste de tables dans le compte de stockage

Récupérer une liste de tables dans le compte de stockage à l’aide [Get-AzStorageTable](/powershell/module/az.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Récupérer une référence à une table spécifique

Pour effectuer des opérations sur une table, vous avez besoin d’une référence à cette table. Obtenir une référence à l’aide [Get-AzStorageTable](/powershell/module/az.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Propriété CloudTable de référence d’une table spécifique

> [!IMPORTANT]
> Utilisation de CloudTable est obligatoire lorsque vous travaillez avec **AzureRmStorageTable** module PowerShell. Appelez le **Get-AzTableTable** commande pour obtenir la référence à cet objet. Cette commande crée également la table si elle n’existe pas déjà.

Pour effectuer des opérations sur une table à l’aide **AzureRmStorageTable**, vous avez besoin d’une référence à la propriété CloudTable d’une table spécifique.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Suppression d’une table

Pour supprimer une table, utilisez [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Cette applet de commande supprime la table, données comprises.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous avez créé un groupe de ressources et un compte de stockage au début de cet article sur les procédures, vous pouvez supprimer toutes les ressources que vous avez créées dans cet exercice en supprimant le groupe de ressources. Cette commande supprime toutes les ressources contenues dans le groupe de ressources, ainsi que le groupe lui-même.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Cet article sur les procédures vous a présenté des opérations Stockage Table Azure courantes réalisables avec PowerShell. Vous avez notamment appris à effectuer les tâches suivantes : 

> [!div class="checklist"]
> * Création d’une table
> * Récupération d’une table
> * Ajout d’entités de table
> * Interrogation d’une table
> * Suppression d’entités de table
> * Suppression d’une table

Pour plus d’informations, consultez les articles suivants :

* [Applets de commande PowerShell - Stockage](/powershell/module/az.storage#storage)

* [Utilisation de Tables Azure à partir de PowerShell - AzureRmStorageTable PS Module v2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) est une application autonome et gratuite de Microsoft qui vous permet d’exploiter visuellement les données de Stockage Azure sur Windows, macOS et Linux.
