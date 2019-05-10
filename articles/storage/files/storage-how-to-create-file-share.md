---
title: Création d’un partage de fichiers Azure | Microsoft Docs
description: Créer un partage de fichiers Azure dans Azure Files à l’aide du portail Azure, de PowerShell et d’Azure CLI.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d945d5b79c274aa8e142203c56b27eb673e36741
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510514"
---
# <a name="create-a-file-share-in-azure-files"></a>Créer un partage de fichiers dans Azure Files
Vous pouvez créer des partages de fichiers Azure à l’aide du  [portail Azure](https://portal.azure.com/), des applets de commande PowerShell du Stockage Azure, des bibliothèques clientes du Stockage Azure ou de l’API REST du Stockage Azure. Ce tutoriel vous apprendra à effectuer les opérations suivantes :
* Création d’un partage de fichiers Azure avec le portail Azure
* [Création d’un partage de fichiers Azure avec PowerShell](#create-file-share-through-powershell)
* [Création d’un partage de fichiers Azure avec l’interface CLI](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Conditions préalables
Pour créer un partage de fichiers Azure, vous pouvez utiliser un compte de stockage existant ou [créer un compte de stockage Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pour créer un partage de fichiers Azure avec PowerShell, vous avez besoin de la clé de compte et du nom de votre compte de stockage. Si vous envisagez d’utiliser Powershell ou l’interface CLI, vous aurez besoin de la clé du compte de stockage.

## <a name="create-a-file-share-through-the-azure-portal"></a>Créer un partage de fichiers via le portail Azure
1. **Accédez au panneau Compte de stockage dans le portail Azure** :    
    ![Panneau Compte de stockage](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Cliquez sur Ajouter un partage de fichiers** :    
    ![Cliquez sur Ajouter un partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Indiquez le nom et le quota. La valeur maximale du quota est de 5 Tio** :    
    ![Indiquez un nom et le quota souhaité pour le nouveau partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal3.png).

4. **Affichez le nouveau partage de fichiers** :  ![Afficher le nouveau partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Chargez un fichier** :  ![Charger un fichier](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Parcourez le partage de fichiers et gérez vos répertoires et vos fichiers** :  ![Parcourir le partage de fichiers](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Création d’un partage de fichiers via PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pour vous préparer à utiliser PowerShell, téléchargez et installez les applets de commande PowerShell Azure. Pour obtenir des instructions sur l’installation et le point d’installation, consultez  [Installer et configurer Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) .

> [!Note]  
> Il est recommandé de télécharger et d’installer le dernier module Azure PowerShell ou d’effectuer une mise à niveau vers celui-ci.

1. **Créez un contexte pour le compte de stockage et la clé**. Le contexte regroupe la clé de compte et le nom du compte de stockage. Pour obtenir des instructions sur la copie de votre clé de compte à partir du  [Portail Azure](https://portal.azure.com/), consultez  [Clés d’accès au compte de stockage](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Créez un nouveau partage de fichiers** :    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Le nom de votre partage de fichiers doit être en minuscules. Pour plus d’informations sur le nommage des partages de fichiers et des fichiers, consultez  [Nommage et référencement des partages, répertoires, fichiers et métadonnées](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Création d’un partage de fichiers via l’Interface de ligne de commande (CLI)
1. **Pour utiliser l’interface de ligne de commande (CLI), téléchargez et installez Azure CLI.**  
    Consultez  [Installer Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) et [Bien démarrer avec Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Créez une chaîne de connexion vers le compte de stockage dans lequel vous souhaitez créer le partage.**  
    Remplacez  ```<storage-account>``` et  ```<resource_group>```  par le nom de votre compte de stockage et le groupe de ressources de l’exemple suivant :

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Créer le partage de fichiers**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Connexion et montage du partage de fichiers – Windows](storage-how-to-use-files-windows.md)
* [Connexion et montage du partage de fichiers – Linux](../storage-how-to-use-files-linux.md)
* [Connexion et montage du partage de fichiers – MacOS](storage-how-to-use-files-mac.md)

Consultez ces liens pour en savoir plus sur Azure Files.

* [FORUM AUX QUESTIONS](../storage-files-faq.md)
* [Résolution des problèmes sur Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Résolution des problèmes sur Linux](storage-troubleshoot-linux-file-connection-problems.md)   
