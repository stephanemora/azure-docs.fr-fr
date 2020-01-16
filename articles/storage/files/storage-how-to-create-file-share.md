---
title: Crée un partage de fichiers Azure
titleSuffix: Azure Files
description: Guide pratique pour créer un partage de fichiers Azure en utilisant le portail Azure, PowerShell ou l’interface Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452889"
---
# <a name="create-a-file-share"></a>Créer un partage de fichiers
Vous pouvez créer un partage dans Azure Files en utilisant le  [portail Azure](https://portal.azure.com/), les applets de commande PowerShell du Stockage Azure, les bibliothèques clientes du Stockage Azure ou l’API REST du Stockage Azure. Dans cet article, vous allez apprendre à :
- Créer un partage de fichiers Azure en utilisant le portail Azure
- Créer un partage de fichiers Azure en utilisant PowerShell
- Créer un partage de fichiers Azure en utilisant l’interface Azure CLI

## <a name="prerequisites"></a>Conditions préalables requises
Pour créer un partage de fichiers Azure, vous pouvez utiliser un compte de stockage existant ou [créer un compte de stockage Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pour créer un partage de fichiers Azure avec PowerShell, vous avez besoin de la clé de compte et du nom de votre compte de stockage. Vous aurez besoin d’une clé de compte de stockage si vous envisagez d’utiliser PowerShell ou l’interface CLI.

> [!NOTE]
> Si vous voulez créer des partages de fichiers supérieurs à 5 Tio, consultez [Activer les partages de fichiers volumineux](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Créer un partage de fichiers via le portail Azure

1. Accédez au panneau **Compte de stockage** dans le portail Azure.
    ![Panneau Compte de stockage](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Sélectionnez le bouton **+ Partage de fichiers**.
    ![Bouton Ajouter un partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Entrez vos informations pour **Nom** et **Quota**.
    ![Le nom et le quota pour le nouveau partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Affichez votre nouveau partage de fichiers.
    ![Nouveau partage de fichiers dans l’interface utilisateur](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Chargez un fichier.
    ![Bouton Charger dans la barre de navigation](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Parcourez votre partage de fichiers, puis gérez vos répertoires et vos fichiers.
    ![Affichage des dossiers de partages de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Créer un partage de fichiers par le biais de PowerShell

Téléchargez et installez les applets de commande Azure PowerShell. Pour obtenir des instructions sur l’installation et le point d’installation, consultez  [Installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

> [!Note]  
> Nous vous recommandons de télécharger et d’installer le dernier module Azure PowerShell, ou d’effectuer une mise à niveau vers celui-ci.

1. Créez un nouveau compte de stockage.
    Un compte de stockage est un pool de stockage partagé où vous pouvez déployer des partages de fichiers Azure et des ressources de stockage comme les objets blob ou les files d’attente.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Créez un partage de fichiers.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> Le nom de votre partage de fichiers doit être en minuscules. Pour obtenir des informations détaillées sur le nommage des partages de fichiers et des fichiers, consultez  [Nommage et référencement des partages, des répertoires, des fichiers et des métadonnées](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-a-file-share-through-the-cli"></a>Créer un partage de fichiers par le biais du portail Azure

1. Téléchargez et installez l’interface Azure CLI.
    Consultez  [Installer l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) et [Bien démarrer avec l’interface Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

1. Créez une chaîne de connexion au compte de stockage dans lequel vous souhaitez créer le partage.
    Remplacez  ```<storage-account>``` et  ```<resource_group>```  par le nom de votre compte de stockage et le groupe de ressources de l’exemple suivant :

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Créez le partage de fichiers.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Connecter et monter un partage de fichiers sur Windows](storage-how-to-use-files-windows.md)
* [Connecter et monter un partage de fichiers sur Linux](storage-how-to-use-files-linux.md)
* [Connecter et monter un partage de fichiers sur macOS](storage-how-to-use-files-mac.md)

Consultez ces liens pour en savoir plus sur Azure Files :

* [Questions fréquentes (FAQ) sur les fichiers de stockage](storage-files-faq.md)
* [Résoudre les problèmes d’Azure Files sous Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Résoudre les problèmes d’Azure Files sous Linux](storage-troubleshoot-linux-file-connection-problems.md)
