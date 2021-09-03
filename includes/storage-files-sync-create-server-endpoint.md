---
title: Fichier Include
description: Fichier include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 6/01/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 47b520fb4817f8fb8e780d52ef7a040617fdd759
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768591"
---
# <a name="portal"></a>[Portail](#tab/azure-portal)
Pour ajouter un point de terminaison de serveur, accédez au nouveau groupe de synchronisation, puis sélectionnez **Ajouter un point de terminaison de serveur**.

![Ajouter un nouveau point de terminaison de serveur dans le volet Groupe de synchronisation](media/storage-files-sync-create-server-endpoint/add-server-endpoint.png)

Le volet **Ajouter un point de terminaison de serveur** s’ouvre, entrez les informations suivantes pour créer un point de terminaison de serveur :

- **Serveur inscrit** : nom du serveur ou du cluster où vous voulez créer le point de terminaison de serveur.
- **Chemin d’accès** : chemin d’accès Windows Server à synchroniser en tant qu’élément du groupe de synchronisation.
- **Hiérarchisation cloud** : commutateur pour activer ou désactiver la hiérarchisation cloud. La hiérarchisation cloud permet de hiérarchiser les fichiers rarement utilisés dans Azure Files.
- **Espace libre du volume** : quantité d’espace libre à réserver sur le volume sur lequel se trouve le point de terminaison de serveur. Par exemple, si l’espace libre du volume est défini à 50 % sur un volume ayant un seul point de terminaison de serveur, environ la moitié de la quantité de données est hiérarchisée dans Azure Files. Que la hiérarchisation cloud soit activée ou non, le partage de fichiers Azure dispose toujours d’une copie complète des données dans le groupe de synchronisation.
- **Mode de téléchargement initial** : une sélection facultative, qui peut être utile lorsque vous avez des fichiers dans le partage de fichiers Azure, mais pas sur le serveur. Une telle situation peut exister, par exemple, si vous créez un point de terminaison de serveur pour ajouter un autre serveur de filiale à un groupe de synchronisation ou lorsque vous récupérez un serveur défaillant en cas de sinistre. Si la hiérarchisation cloud est activée, le comportement par défaut consiste à rappeler uniquement l’espace de noms, pas de contenu de fichier initialement. Cela est utile si vous pensez que les demandes d’accès des utilisateurs doivent décider du contenu du fichier qui est rappelé sur le serveur. Si la hiérarchisation cloud est désactivée, le comportement par défaut est que l’espace de noms sera d’abord téléchargé, puis les fichiers seront rappelés en fonction du timestamp de la dernière modification jusqu’à ce que la capacité locale soit atteinte. Vous pouvez toutefois changer le mode de téléchargement initial sur Espace de noms uniquement. Un troisième mode ne peut être utilisé que si la hiérarchisation cloud est désactivée pour ce point de terminaison de serveur. Ce mode évite de rappeler l’espace de noms d’abord. Les fichiers s’affichent uniquement sur le serveur local s’ils ont pu être téléchargés entièrement. Ce mode est utile si, par exemple, une application nécessite la présence de fichiers complets et ne peut pas tolérer des fichiers hiérarchisés dans son espace de noms.

Pour ajouter le point de terminaison de serveur, sélectionnez **Créer**. Vos fichiers sont maintenant synchronisés entre le partage de fichiers Azure et Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Exécutez les commandes PowerShell suivantes pour créer le point de terminaison de serveur, et veillez à remplacer `<your-server-endpoint-path>`, `<your-volume-free-space>` par les valeurs souhaitées et vérifiez les paramètres pour les stratégies facultatives [téléchargement initial](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-download-section) et [chargement initial](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-sync-section).

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly
$initialUploadPolicy = Merge
# Optional property. Choose from: [Merge] default for all new server endpoints. Content from the server and the cloud merge. This is the right choice if one location is empty or other server endpoints already exist in the sync group. [ServerAuthoritative] This is the right choice when you seeded the Azure file share (e.g. with Data Box) AND you are connecting the server location you seeded from. This enables you to catch up the Azure file share with the changes that happened on the local server since the seeding.

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy `
        -InitialUploadPolicy $initialUploadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour créer un nouveau point de terminaison serveur, utilisez la commande [`az storagesync sync-group server-endpoint`](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create).

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --initial-upload-policy Merge [OR] ServerAuthoritative

```

---