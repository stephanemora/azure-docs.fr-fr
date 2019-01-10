---
title: Déployer Azure File Sync | Microsoft Docs
description: Découvrez comment déployer Azure File Sync, du début à la fin.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: eeaedc84e860cebc0b001300ace4fe1594375af2
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999405"
---
# <a name="deploy-azure-file-sync"></a>Déployer Azure File Sync
Utilisez Azure File Sync pour centraliser les partages de fichiers de votre organisation dans Azure Files tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Nous vous recommandons fortement de lire les articles [Planification d’un déploiement Azure Files](storage-files-planning.md) et [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md) avant d’effectuer les étapes décrites dans cet article.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis
* Avoir un compte de stockage Azure et un partage de fichiers Azure dans la même région où vous souhaitez déployer Azure File Sync. Pour plus d'informations, consultez les pages suivantes :
    - [Disponibilité des régions](storage-sync-files-planning.md#region-availability) pour Azure File Sync.
    - [Créer un compte de stockage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) pour obtenir une procédure pas à pas de la création d’un compte de stockage.
    - [Créer un partage de fichiers](storage-how-to-create-file-share.md) pour obtenir une procédure pas à pas de la création d’un partage de fichiers.
* Avoir au moins une instance de Windows Server ou d’un cluster Windows Server prise en charge pour la synchronisation avec Azure File Sync. Pour plus d’informations sur les versions de Windows Server prises en charge, consultez [Interopérabilité avec Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Vérifiez que PowerShell 5.1 est installé sur votre serveur Windows. Si vous utilisez Windows Server 2012 R2, vérifiez que vous exécutez au moins PowerShell 5.1.\*. Vous pouvez ignorer cette vérification sur Windows Server 2016, car PowerShell 5.1 est la version intégrée par défaut. Sur Windows Server 2012 R2, vous pouvez vérifier que vous exécutez PowerShell 5.1.\* en regardant la valeur de la propriété **PSVersion** de l’objet **$PSVersionTable** :

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Si la valeur PSVersion est inférieure à 5.1.\*, comme cela est le cas avec la plupart des nouvelles installations de Windows Server 2012 R2, vous pouvez facilement effectuer la mise à niveau en téléchargeant et en installant [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Le package correct à télécharger et à installer pour Windows Server 2012 R2 est **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > Azure File Sync ne prend pas encore en charge PowerShell 6 sur Windows Server 2012 R2 ou Windows Server 2016.
* Le module Azure PowerShell sur les serveurs à utiliser avec Azure File Sync. Pour plus d’informations sur l’installation des modules Azure PowerShell, consultez [Installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Nous recommandons toujours d’utiliser la dernière version des modules Azure PowerShell. 

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Préparer Windows Server pour une utilisation avec Azure File Sync
Pour chaque serveur que vous souhaitez utiliser avec Azure File Sync, y compris chaque nœud de serveur dans un cluster de basculement, vous devez désactiver **Configuration de sécurité renforcée d’Internet Explorer**. Cela est nécessaire uniquement pour l’inscription initiale du serveur. Vous pouvez réactiver cette configuration une fois que le serveur a été inscrit.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Ouvrez le Gestionnaire de serveurs.
2. Cliquez sur **Serveur Local** :  
    ![« Serveur local » sur le côté gauche de l’interface utilisateur du Gestionnaire de serveur](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Dans le sous-volet **Propriétés**, sélectionnez le lien vers **Configuration de sécurité renforcée d’Internet Explorer**.  
    ![Volet « Configuration de sécurité renforcée d’Internet Explorer » dans l’interface utilisateur du Gestionnaire de serveur](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Dans la boîte de dialogue **Configuration de sécurité renforcée d’Internet Explorer**, sélectionnez **Désactivé** pour **Administrateurs** et **Utilisateurs** :  
    ![Fenêtre indépendante Configuration de sécurité renforcée d’Internet Explorer avec l’option « Désactivé » sélectionnée](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Pour désactiver la configuration de sécurité renforcée d’Internet Explorer, exécutez la commande suivante à partir d’une session PowerShell avec élévation de privilèges :

```PowerShell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="install-the-azure-file-sync-agent"></a>Installer l’agent Azure File Sync
L’agent Azure File Sync est un package téléchargeable qui permet à Windows Server de rester synchronisé avec un partage de fichiers Azure. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Vous pouvez télécharger l’agent à partir du [Centre de téléchargement Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). À la fin du téléchargement, double-cliquez sur le package MSI pour démarrer l’installation de l’agent Azure File Sync.

> [!Important]  
> Si vous comptez utiliser Azure File Sync avec un cluster de basculement, l’agent Azure File Sync doit être installé sur chaque nœud du cluster. Chaque nœud du cluster doit être inscrit pour fonctionner avec Azure File Sync.

Nous vous recommandons de procéder comme suit :
- Conservez le chemin d’installation par défaut (C:\Program Files\Azure\StorageSyncAgent), pour simplifier le dépannage et la maintenance du serveur.
- Activez Microsoft Update pour garantir la mise à jour d’Azure File Sync. Toutes les mises à jour de l’agent Azure File Sync, y compris les correctifs logiciels et les mises à jour de fonctionnalités, s’effectuent à partir de Microsoft Update. Nous vous recommandons d’installer la dernière mise à jour d’Azure File Sync. Pour plus d’informations, consultez [Stratégie de mise à jour d’Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Au terme de l’installation de l’agent Azure File Sync, l’interface utilisateur d’inscription du serveur s’ouvre automatiquement. Vous devez disposer d’un service de synchronisation de stockage avant de procéder à l’inscription. Consultez la section suivante sur la création d’un service de synchronisation de stockage.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Exécutez le code PowerShell suivant pour télécharger la version appropriée de l’agent Azure File Sync pour votre système d’exploitation et l’installer sur votre système.

> [!Important]  
> Si vous comptez utiliser Azure File Sync avec un cluster de basculement, l’agent Azure File Sync doit être installé sur chaque nœud du cluster. Chaque nœud du cluster doit être inscrit pour fonctionner avec Azure File Sync.

```PowerShell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875004 `
        -OutFile "StorageSyncAgent.exe" 
}
elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875002 `
        -OutFile "StorageSyncAgent.exe" 
}
else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2 and Windows Server 2016")
}

# Extract the MSI from the install package
$tempFolder = New-Item -Path "afstemp" -ItemType Directory
Start-Process -FilePath ".\StorageSyncAgent.exe" -ArgumentList "/C /T:$tempFolder" -Wait

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "$($tempFolder.FullName)\StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.exe", ".\afstemp" -Recurse -Force
```

---

## <a name="deploy-the-storage-sync-service"></a>Déployer le service de synchronisation de stockage 
Le déploiement de Azure File Sync commence par le placement d’une ressource **Service de synchronisation de stockage** dans un groupe de ressources de votre abonnement sélectionné. Nous vous recommandons d’en approvisionner le plus petit nombre possible. Vous allez créer une relation d’approbation entre vos serveurs et cette ressource et un serveur ne peut être inscrit qu’auprès d’un seul service de synchronisation de stockage. Par conséquent, il est recommandé de déployer des services de synchronisation de stockage autant que nécessaire pour séparer les groupes de serveurs. Gardez à l’esprit que les serveurs de services de synchronisation de stockage différents ne peuvent pas être synchronisés entre eux.

> [!Note]
> Le service de synchronisation de stockage a hérité des autorisations d’accès de l’abonnement et du groupe de ressources dans lequel il a été déployé. Nous vous recommandons de vérifier soigneusement les personnes qui y ont accès. Les entités avec un accès en écriture peuvent démarrer la synchronisation de nouveaux ensembles de fichiers à partir des serveurs inscrits dans ce service de synchronisation de stockage et permettre le flux de données vers le stockage Azure qui leur est accessible.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Pour déployer un service de synchronisation de stockage, accédez au [portail Azure](https://portal.azure.com/), cliquez sur *Nouveau*, puis recherchez Azure File Sync. Dans les résultats de la recherche, sélectionnez **Azure File Sync**, puis sélectionnez **Créer** pour ouvrir l’onglet **Déployer la synchronisation du stockage**.

Dans le volet qui s’ouvre, entrez les informations suivantes :

- **Nom** : Nom unique (par abonnement) du service de synchronisation de stockage.
- **Abonnement**: abonnement dans lequel vous voulez créer le service de synchronisation de stockage. Selon la stratégie de configuration de votre organisation, vous avez accès à un ou plusieurs abonnements. Un abonnement Azure est le conteneur de base pour la facturation de chaque service cloud (par exemple, Azure Files).
- **Groupe de ressources** : un groupe de ressources est un groupe logique de ressources Azure, tel qu’un compte de stockage ou un service de synchronisation de stockage. Vous pouvez créer un groupe de ressources ou utiliser un groupe de ressources existant pour Azure File Sync. (Nous vous recommandons d’utiliser des groupes de ressources comme conteneurs pour isoler les ressources logiquement dans votre organisation, par exemple, en regroupant les ressources humaines ou les ressources d’un projet spécifique.)
- **Emplacement** : région dans laquelle vous souhaitez déployer Azure File Sync. Seuls les régions prises en charge sont disponibles dans cette liste.

Quand vous avez terminé, sélectionnez **Créer** pour déployer le service de synchronisation de stockage.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Avant d’interagir avec les applets de commande de gestion de Azure File Sync, vous devez importer une DLL et créer un contexte d’administration Azure File Sync. C’est obligatoire, car les applets de commande de gestion d’Azure File Sync ne font pas encore partie des modules Azure PowerShell.

> [!Note]  
> Le package StorageSync.Management.PowerShell.Cmdlets.dll, qui contient les applets de commande de gestion de Azure File Sync, contient (intentionnellement) un applet de commande avec un verbe non approuvé (`Login`). Le nom `Login-AzureStorageSync` a été choisi pour correspondre à l’alias d’applet de commande `Login-AzAccount` dans le module Azure PowerShell. Ce message d’erreur (et l’applet de commande) sont supprimés quand l’agent Azure File Sync est ajouté au module Azure PowerShell.

```PowerShell
$acctInfo = Login-AzAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    -SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

Une fois que vous avez créé le contexte Azure File Sync avec l’applet de commande `Login-AzureR,StorageSync`, vous pouvez créer le service de synchronisation de stockage. Veillez à remplacer `<my-storage-sync-service>` par le nom de votre service de synchronisation de stockage.

```PowerShell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Inscrire un serveur Windows Server au service de synchronisation de stockage
L’inscription de votre serveur Windows Server à un service de synchronisation de stockage établit une relation d’approbation entre votre serveur (ou cluster) et le service de synchronisation de stockage. Un serveur ne peut être inscrit qu’auprès d’un seul service de synchronisation du stockage et peut se synchroniser avec d’autres serveurs et avec des partages de fichiers Azure associés avec le même service de synchronisation de stockage.

> [!Note]
> L’inscription du serveur utilise vos informations d’identification Azure pour créer une relation d’approbation entre le service de synchronisation du stockage et votre serveur Windows, toutefois par la suite le serveur crée et utilise sa propre identité qui est valide tant que le serveur reste inscrit et le jeton de signature d’accès partagé actuel (stockage SAS) est valide. Un nouveau jeton SAS ne peut pas être émis sur le serveur une fois le serveur désinscrit, retirant ainsi la capacité du serveur à accéder à vos partages de fichiers Azure, arrêtant toute synchronisation.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
L’interface utilisateur d’inscription du serveur s’ouvre normalement automatiquement après l’installation de l’agent Azure File Sync. Si ce n’est pas le cas, vous pouvez l’ouvrir manuellement à partir de son emplacement de fichier : C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quand l’interface utilisateur d’inscription du serveur est ouverte, sélectionnez **Se connecter** pour commencer.

Une fois que vous êtes connecté, vous êtes invité à fournir les informations suivantes :

![Capture d’écran de l’interface utilisateur d’inscription de serveur](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Abonnement Azure** : abonnement qui contient le service de synchronisation de stockage (consultez [Déployer le service de synchronisation de stockage](#deploy-the-storage-sync-service)). 
- **Groupe de ressources** : groupe de ressources qui contient le service de synchronisation de stockage.
- **Service de synchronisation de stockage** : nom du service de synchronisation de stockage auquel vous souhaitez vous inscrire.

Après avoir sélectionné les informations appropriées, sélectionnez **S’inscrire** pour effectuer l’inscription du serveur. Dans le cadre du processus d’inscription, vous êtes invité à effectuer une nouvelle connexion.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Créer un groupe de synchronisation et un point de terminaison du cloud
Un groupe de synchronisation définit la topologie de synchronisation d’un ensemble de fichiers. Les points de terminaison dans un groupe de synchronisation sont synchronisés entre eux. Un groupe de synchronisation doit contenir un seul point de terminaison cloud, qui représente un partage de fichiers Azure, et un ou plusieurs points de terminaison de serveur. Un point de terminaison de serveur représente un chemin d’accès vers le serveur inscrit. Un serveur peut avoir des points de terminaison de serveur dans plusieurs groupes de synchronisation. Vous pouvez créer autant de groupes de synchronisation que nécessaire pour décrire de manière appropriée votre topologie de synchronisation.

Un point de terminaison cloud est un pointeur vers un partage de fichiers Azure. Tous les points de terminaison de serveur seront synchronisés avec un point de terminaison cloud, en faisant ainsi un concentrateur. Le compte de stockage pour le partage de fichiers Azure doit se trouver dans la même région que le service de synchronisation de stockage. L’intégralité du partage de fichiers Azure est synchronisée, à une exception près : un dossier spécial, comparable au dossier « System Volume Information » masqué sur un volume NTFS, peut être configuré. Ce répertoire est appelé «.SystemShareInformation». Il contient des métadonnées de synchronisation importantes qui ne seront pas synchronisées avec d’autres points de terminaison. Ne pas l’utiliser ni le supprimer !

> [!Important]  
> Vous pouvez apporter des modifications à un point de terminaison cloud ou un point de terminaison de serveur dans le groupe de synchronisation, et synchroniser vos fichiers avec les autres points de terminaison du groupe de synchronisation. Si vous apportez une modification au point de terminaison cloud (partage de fichiers Azure) directement, cette modification doit être détectée au préalable par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud toutes les 24 heures uniquement. Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur Azure Files](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Pour créer un groupe de synchronisation, dans le [portail Azure](https://portal.azure.com/), accédez à votre service de synchronisation de stockage, puis sélectionnez **+ Groupe de synchronisation** :

![Créer un groupe de synchronisation dans le portail Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Dans le volet qui s’ouvre, entrez les informations suivantes pour créer un groupe de synchronisation avec un point de terminaison cloud :

- **Nom du groupe de synchronisation** : nom du groupe de synchronisation à créer. Ce nom doit être unique au sein du service de synchronisation de stockage, mais vous pouvez choisir tout nom ayant un sens pour vous.
- **Abonnement**: abonnement dans lequel vous avez déployé le service de synchronisation de stockage, comme décrit dans [Déployer le service de synchronisation de stockage](#deploy-the-storage-sync-service).
- **Compte de stockage** : si vous sélectionnez **Sélectionner un compte de stockage**, un autre volet s’affiche dans lequel vous pouvez sélectionner le compte de stockage qui contient le partage de fichiers Azure avec lequel effectuer la synchronisation.
- **Partage de fichiers Azure** : nom du partage de fichiers Azure avec lequel vous voulez effectuer la synchronisation.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Pour créer le groupe de synchronisation, exécutez la commande PowerShell suivante. N’oubliez pas de remplacer `<my-sync-group>` par le nom du groupe de synchronisation.

```PowerShell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

Une fois le groupe de synchronisation créé avec succès, vous pouvez créer votre point de terminaison cloud. Veillez à remplacer `<my-storage-account>` et `<my-file-share>` par les valeurs attendues.

```PowerShell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id `
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Créer un point de terminaison de serveur
Un point de terminaison de serveur représente un emplacement spécifique sur un serveur inscrit, comme un dossier sur un volume de serveur. Un point de terminaison de serveur doit être un chemin d’accès sur un serveur inscrit (plutôt que sur un partage monté), et pour utiliser la hiérarchisation cloud, le chemin d’accès doit être sur un volume non système. Le stockage attaché au réseau (NAS) n’est pas pris en charge.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Pour ajouter un point de terminaison de serveur, accédez au nouveau groupe de synchronisation, puis sélectionnez **Ajouter un point de terminaison de serveur**.

![Ajouter un nouveau point de terminaison de serveur dans le volet Groupe de synchronisation](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Dans le volet **Ajouter un point de terminaison de serveur**, entrez les informations suivantes pour créer un point de terminaison de serveur :

- **Serveur inscrit** : nom du serveur ou du cluster où vous voulez créer le point de terminaison de serveur.
- **Chemin d’accès** : chemin d’accès Windows Server à synchroniser en tant qu’élément du groupe de synchronisation.
- **Hiérarchisation cloud** : commutateur pour activer ou désactiver la hiérarchisation cloud. La hiérarchisation cloud permet de hiérarchiser les fichiers rarement utilisés dans Azure Files.
- **Espace libre du volume** : quantité d’espace libre à réserver sur le volume sur lequel se trouve le point de terminaison de serveur. Par exemple, si l’espace libre du volume est défini à 50 % sur un volume ayant un seul point de terminaison de serveur, environ la moitié de la quantité de données est hiérarchisée dans Azure Files. Que la hiérarchisation cloud soit activée ou non, le partage de fichiers Azure dispose toujours d’une copie complète des données dans le groupe de synchronisation.

Pour ajouter le point de terminaison de serveur, sélectionnez **Créer**. Vos fichiers sont maintenant synchronisés entre le partage de fichiers Azure et Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Exécutez les commandes PowerShell suivantes pour créer le point de terminaison de serveur, et veillez à remplacer `<your-server-endpoint-path>` et `<your-volume-free-space>` par les valeurs souhaitées.

```PowerShell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering $true `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
}
else {
    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Intégrer Azure File Sync
Voici les étapes recommandées pour intégrer Azure File Sync sans aucun temps d’arrêt tout en préservant toute la fidélité du fichier et la liste de contrôle d’accès (ACL) :
 
1. Déployez un service de synchronisation du stockage.
2. Créez un groupe de synchronisation.
3. Installez l’agent Azure File Sync sur le serveur comportant le jeu de données complet.
4. Inscrivez ce serveur et créez un point de terminaison de serveur sur le partage. 
5. Laissez la synchronisation effectuer la totalité du chargement sur le partage de fichiers Azure (point de terminaison cloud).  
6. Une fois le chargement initial terminé, installez l’agent Azure File Sync sur chacun des serveurs restants.
7. Créez de nouveaux partages de fichiers sur tous les serveurs restants.
8. Créez des points de terminaison de serveur sur les nouveaux partages de fichiers avec une stratégie de hiérarchisation cloud, si vous le souhaitez. (Cette étape requiert du stockage supplémentaire disponible pour la configuration initiale.)
9. Laissez l’agent Azure File Sync effectuer une restauration rapide de l’espace de noms complet sans transfert de données réel. Après la synchronisation de l’espace de noms complet, le moteur de synchronisation remplira l’espace disque local en fonction de la stratégie de hiérarchisation cloud du point de terminaison de serveur. 
10. Vérifiez la synchronisation est terminée et testez votre topologie comme vous le souhaitez. 
11. Redirigez les utilisateurs et les applications vers ce nouveau partage.
12. Il est possible quoique non obligatoire de supprimer les partages en double sur les serveurs.
 
Si vous n’avez pas de stockage supplémentaire pour l’intégration initiale et que vous souhaitez utiliser les partages existants, vous pouvez préamorcer les données dans les partages de fichiers Azure. Cette approche est proposée si et seulement si vous êtes en mesure d’accepter des temps d’arrêt et de garantir l’absence totale de modification des données sur les partages du serveur pendant le processus d’intégration initial. 
 
1. Assurez-vous que les données des différents serveurs ne changeront pas au cours du processus d’intégration.
2. Préamorcez les partages de fichiers Azure avec les données du serveur à l’aide d’un outil de transfert de données sur le SMB, par exemple, Robocopy ou SMB Direct. AzCopy ne chargeant pas les données sur le SMB, il ne peut pas être utilisé pour le préamorçage.
3. Créez une topologie Azure File Sync avec les points de terminaison de serveur souhaités pointant sur les partages existants.
4. Laissez la synchronisation terminer le processus de rapprochement sur tous les points de terminaison. 
5. Une fois le rapprochement terminé, vous pourrez ouvrir les partages pour les modifier.
 
Notez que l’approche par préamorçage possède actuellement quelques limitations. 
- La fidélité optimale sur les fichiers n’est pas conservée. Par exemple, les fichiers perdent les ACL et les timestamps.
- Les modifications de données effectuées sur le serveur avant que la topologie de synchronisation ne soit entièrement opérationnelle risquent de provoquer des conflits sur les points de terminaison de serveur.  
- Une fois le point de terminaison cloud créé, Azure File Sync exécute un processus de détection des fichiers dans le cloud avant de démarrer la synchronisation initiale. Le temps nécessaire à ce processus varie en fonction de différents facteurs, comme la vitesse du réseau, la bande passante disponible et le nombre de fichiers et de dossiers. Pour donner une estimation approximative dans la préversion, le processus de détection s’exécute approximativement à une vitesse de 10 fichiers/s. Par conséquent, même si le préamorçage est rapide, le délai global nécessaire pour obtenir un système entièrement opérationnel peut se révéler beaucoup plus long lorsque les données sont préamorcées dans le cloud.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrer un déploiement de la réplication DFS (DFS-R) vers Azure File Sync
Pour migrer un déploiement de DFS-R vers Azure File Sync :

1. Créez un groupe de synchronisation pour représenter la topologie DFS-R que vous remplacez.
2. Démarrez sur le serveur contenant le jeu de données complet de votre topologie DFS-R à migrer. Installez Azure File Sync sur ce serveur.
3. Inscrivez ce serveur et créez un point de terminaison de serveur pour le premier serveur à migrer. N’activez pas la hiérarchisation cloud.
4. Laissez l’ensemble de la synchronisation des données sur votre partage de fichiers Azure (point de terminaison de cloud).
5. Installez et inscrivez l’agent Azure File Sync sur tous les autres serveurs DFS-R.
6. Désactivez DFS-R. 
7. Créez un point de terminaison de serveur sur tous les serveurs DFS-R. N’activez pas la hiérarchisation cloud.
8. Vérifiez la synchronisation est terminée et testez votre topologie comme vous le souhaitez.
9. Mettez DFS-R hors service.
10. La hiérarchisation cloud peut maintenant être activée sur n’importe quel point de terminaison de serveur souhaité.

Pour plus d’informations, consultez [Interopérabilité d’Azure File Sync avec le système de fichiers DFS](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Étapes suivantes
- [Ajouter ou supprimer un point de terminaison de serveur pour Azure File Sync](storage-sync-files-server-endpoint.md)
- [Inscrire ou désinscrire un serveur auprès d’Azure File Sync](storage-sync-files-server-registration.md)