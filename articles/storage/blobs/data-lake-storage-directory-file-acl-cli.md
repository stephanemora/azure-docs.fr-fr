---
title: Utiliser Azure CLI pour gérer les données (Azure Data Lake Storage Gen2)
description: Utilisez l’interface Azure CLI pour gérer les répertoires et les fichiers dans les comptes de stockage dotés d’un espace de noms hiérarchique.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 08e1976a1bc97ae83d1b9d45700fd614420be0c2
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413066"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Utiliser Azure CLI pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2

Cet article vous explique comment utiliser l’[interface de ligne de commande (CLI) Azure](/cli/azure/) pour créer et gérer les répertoires et les fichiers dans les comptes de stockage dotés d’un espace de noms hiérarchique.

Pour en savoir plus sur la façon d’obtenir, de définir et de mettre à jour les listes de contrôle d’accès (ACL, Access Control List) des répertoires et des fichiers, consultez [Utiliser Azure CLI pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md).

[Exemples](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [Donner votre avis](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- Azure CLI version `2.6.0` ou ultérieure.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Vérifiez que la version correcte d’Azure CLI est installée.

1. Ouvrez [Azure Cloud Shell](../../cloud-shell/overview.md) ou, si vous avez [installé](/cli/azure/install-azure-cli) Azure CLI localement, ouvrez une application console de commandes telle que Windows PowerShell.

2. Vérifiez que la version `2.6.0` d’Azure CLI ou une version supérieure est installée à l’aide de la commande suivante.

   ```azurecli
    az --version
   ```

   Si la version d’Azure CLI est inférieure à `2.6.0`, installez une version plus récente. Voir [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Se connecter au compte

1. Si vous utilisez Azure CLI localement, exécutez la commande login.

   ```azurecli
   az login
   ```

   Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fait et charge une page de connexion Azure par la même occasion.

   Sinon, ouvrez une page de navigateur à l’adresse [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et entrez le code d’autorisation affiché dans votre terminal. Connectez-vous ensuite avec les informations d’identification de votre compte dans le navigateur.

   Pour en savoir plus sur les différentes méthodes d’authentification, consultez [Autoriser l’accès à des données d’objet blob ou de file d’attente avec Azure CLI](./authorize-data-operations-cli.md).

2. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage qui doit héberger votre site web statique.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

> [!NOTE]
> L’exemple présenté dans cet article illustre l’autorisation Azure Active Directory (Azure AD). Pour en savoir plus sur les méthodes d’autorisation, consultez [Autoriser l’accès à des données d’objet blob ou de file d’attente avec Azure CLI](./authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Créez un conteneur.

Un conteneur fait office de système de fichiers pour vos fichiers. Vous pouvez en créer un à l’aide de la commande `az storage fs create`. 

Cet exemple crée un conteneur nommé `my-file-system`.

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Afficher les propriétés d’un conteneur

Vous pouvez imprimer les propriétés d’un conteneur dans la console à l’aide de la commande `az storage fs show`.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Lister le contenu d’un conteneur

Affichez le contenu d’un répertoire à l’aide de la commande `az storage fs file list`.

Cet exemple liste le contenu d’un conteneur nommé `my-file-system`.

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Supprimer un conteneur

Supprimez un conteneur à l’aide de la commande `az storage fs delete`.

Cet exemple supprime un conteneur nommé `my-file-system`. 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire à l’aide de la commande `az storage fs directory create`. 

Cet exemple ajoute un répertoire nommé `my-directory` à un conteneur nommé `my-file-system` qui se trouve dans un compte nommé `mystorageaccount`.

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Afficher les propriétés du répertoire

Vous pouvez imprimer les propriétés d’un répertoire dans la console à l’aide de la commande `az storage fs directory show`.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire à l’aide de la commande `az storage fs directory move`.

Cet exemple affecte au répertoire `my-directory` le nouveau nom `my-new-directory` dans le même conteneur.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Cet exemple déplace un répertoire vers un conteneur nommé `my-second-file-system`.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire à l’aide de la commande `az storage fs directory delete`.

Cet exemple supprime un répertoire nommé `my-directory`. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Vérifier l’existence d’un répertoire

Déterminez si un répertoire spécifique existe dans le conteneur à l’aide de la commande `az storage fs directory exists`.

Cet exemple indique si un répertoire nommé `my-directory` existe dans le conteneur `my-file-system`. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire

Téléchargez un fichier à partir d’un répertoire à l’aide de la commande `az storage fs file download`.

Cet exemple télécharge un fichier nommé `upload.txt` à partir d’un répertoire nommé `my-directory`. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Affichez le contenu d’un répertoire à l’aide de la commande `az storage fs file list`.

Cet exemple liste le contenu d’un répertoire nommé `my-directory` qui se trouve dans le conteneur `my-file-system` d’un compte de stockage nommé `mystorageaccount`. 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire

Téléchargez un fichier dans un répertoire à l’aide de la commande `az storage fs file upload`.

Cet exemple charge un fichier nommé `upload.txt` dans un répertoire nommé `my-directory`. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Afficher les propriétés d’un fichier

Vous pouvez imprimer les propriétés d’un fichier dans la console à l’aide de la commande `az storage fs file show`.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Renommer ou déplacer un fichier

Renommez ou déplacez un fichier à l’aide de la commande `az storage fs file move`.

Cet exemple renomme un fichier du nom `my-file.txt` au nom `my-file-renamed.txt`.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Supprimer un fichier

Supprimez un fichier à l’aide de la commande `az storage fs file delete`.

Cet exemple supprime un fichier nommé `my-file.txt`.

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Voir aussi

- [Exemples](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Envoyer des commentaires](https://github.com/Azure/azure-cli-extensions/issues)
- [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Utiliser Azure CLI pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)
