---
title: Démarrage rapide pour la gestion des partages de fichiers Azure avec Azure PowerShell
description: Utilisez ce guide de démarrage rapide pour découvrir comment gérer les partages de fichiers Azure avec Azure PowerShell.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 16f557d48f8056d438d55fdd066395e7e36ed8a5
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945482"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Démarrage rapide : créer et gérer un partage de fichiers Azure avec Azure PowerShell 
Ce guide vous explique les bases de l’utilisation du [partage de fichiers](storage-files-introduction.md) Azure avec PowerShell. Le partage de fichiers Azure est similaire à d’autres partages de fichiers, mais est stocké dans le cloud et s’appuie sur la plateforme Azure. Il prend en charge le protocole SMB de norme industrielle et permet le partage de fichiers entre plusieurs machines, applications et instances. 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous souhaitez installer et utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.1.1 ou version ultérieure pour les besoins de ce guide. Pour connaître la version du module Azure PowerShell que vous exécutez, utilisez la commande `Get-Module -ListAvailable AzureRM`. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Si vous n’avez pas déjà un groupe de ressources Azure, vous pouvez en créer un avec la cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

L’exemple suivant permet de créer un groupe de ressources nommé *myResourceGroup* dans la région USA Est :

```azurepowershell-interactive
New-AzureRmResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
Un compte de stockage est un pool partagé de stockage que vous pouvez utiliser pour déployer un partage de fichiers Azure, ou d’autres ressources de stockage comme les objets blob ou les files d’attente. Un compte de stockage peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage.

Cet exemple crée un compte de stockage à l’aide de la cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). Le compte de stockage est nommé *mystorageaccount<random number>* et une référence à ce compte de stockage est stockée dans la variable **$storageAcct**. Les noms de compte de stockage doivent être uniques. Par conséquent, utilisez `Get-Random` pour ajouter un numéro au nom pour le rendre unique. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Crée un partage de fichiers Azure
Vous pouvez désormais créer votre premier partage de fichiers Azure. Vous pouvez créer un partage de fichier à l’aide de la cmdlet [New-AzureStorageShare](/powershell/module/azurerm.storage/new-azurestorageshare). Cet exemple crée un partage nommé `myshare`.

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

Les noms de partage ne doivent contenir que des minuscules, des nombres et des traits d’union uniques, mais ne peut commencer par un trait d’union. Pour plus d’informations sur la façon de nommer des partages de fichiers et des fichiers, consultez la rubrique [Affectation de noms et références aux partages, répertoires, fichiers et métadonnées](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Utiliser votre partage de fichiers Azure
Azure Files offre deux méthodes pour utiliser les fichiers et dossiers au sein de votre partage de fichiers Azure : le [protocole Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) de norme industrielle et le [protocole REST de fichier](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Pour monter un partage de fichiers avec SMB, consultez le document suivant en fonction de votre système d’exploitation :
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Utilisation d’un partage de fichiers Azure avec le protocole REST de fichier 
Il est possible de travailler directement avec le protocole de REST de fichier (c’est-à-dire fabriquer vous-même les appels HTTP REST manuellement), mais la méthode la plus courante d’utiliser le protocole REST de fichier consiste à utiliser le module AzureRM PowerShell, [Azure CLI](storage-how-to-use-files-cli.md), ou le Kit de développement logiciel (SDK) du stockage Azure. Toutes ces solutions offrent un bon wrapper autour du protocole REST de fichier dans le langage de script/programmation de votre choix.  

Dans la plupart des cas, vous allez utiliser votre partage de fichiers Azure sur le protocole SMB, car cela vous permet d’utiliser les applications et outils existants que l’on s’attend à utiliser. Toutefois, utiliser l’API REST de fichier plutôt que SMB est avantageux pour plusieurs raisons, par exemple :

- Vous naviguez sur votre partage de fichiers à partir du Cloud Shell PowerShell (qui ne peut pas monter les partages de fichiers sur SMB).
- Vous devez exécuter un script ou application à partir d’un client qui ne peut pas monter de partages SMB, notamment les clients locaux qui n’ont pas débloqué le port 445.
- Vous tirez profit de ressources serverless, comme [Azure Functions](../../azure-functions/functions-overview.md) par exemple. 

Les exemples suivants montrent comment utiliser le module AzureRM PowerShell pour manipuler votre partage de fichiers Azure avec le protocole REST de fichier. 

#### <a name="create-directory"></a>Créer un répertoire
Pour créer un répertoire nommé *myDirectory* à la racine de votre partage de fichiers Azure, utilisez la cmdlet [New-AzureStorageDirectory](/powershell/module/azurerm.storage/new-azurestoragedirectory).

```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Charger un fichier
Pour montrer comment charger un fichier avec la cmdlet [Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent), nous devons d’abord créer un fichier dans le disque de travail de PowerShell Cloud Shell à charger. 

Cet exemple place la date et l’heure actuelles dans un nouveau fichier sur votre disque de travail, puis charge le fichier vers le partage de fichiers.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Si vous exécutez PowerShell en local, vous devez remplacer `C:\Users\ContainerAdministrator\CloudDrive\` par un chemin existant sur votre machine.

Après avoir chargé le fichier, vous pouvez exécuter la cmdlet [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) pour vérifier que le fichier a bien été chargé dans votre partage de fichiers Azure. 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Téléchargement d’un fichier
Vous pouvez exécuter la cmdlet [Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) pour télécharger une copie du fichier que vous venez de charger sur le disque de travail de Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Une fois le fichier téléchargé, vous pouvez utiliser `Get-ChildItem` pour voir si le fichier a été téléchargé sur votre disque de travail de PowerShell Cloud Shell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Copie des fichiers
Une tâche courante consiste à copier les fichiers d’un partage de fichiers vers un autre partage de fichiers, ou dans/depuis un conteneur de stockage d’objets blob Azure. Pour illustrer cette fonctionnalité, vous pouvez créer un partage et copier le fichier que vous venez de charger dans ce nouveau partage avec la cmdlet [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy). 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Maintenant, si vous répertoriez les fichiers dans le nouveau partage, vous devez voir votre fichier copié.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Bien que la cmdlet `Start-AzureStorageFileCopy` soit pratique pour déplacer des fichiers ad-hoc entre des partages de fichiers Azure et des conteneurs de stockage d’objets blob Azure, nous recommandons AzCopy pour des déplacements plus volumineux (en termes de nombre ou de taille des fichiers déplacés). En savoir plus sur [AzCopy pour Windows](../common/storage-use-azcopy.md) et [AzCopy pour Linux](../common/storage-use-azcopy-linux.md). AzCopy doit être installé en local (il n’est pas disponible dans Cloud Shell). 

## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous avez terminé, vous pouvez utiliser la cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources associées. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Vous pouvez aussi supprimer les ressources une par une :

- Pour supprimer les partages de fichiers Azure que nous avons créés pour ce guide de démarrage rapide.
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- Pour supprimer le compte de stockage (ce qui supprimera par la même occasion les partages de fichiers créés ainsi que toutes les autres ressources de stockage créées telles qu’un conteneur de stockage d’objets blob Azure).
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Qu’est-ce qu’Azure Files ?](storage-files-introduction.md)