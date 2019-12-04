---
title: Utiliser Azure CLI pour les fichiers et les listes de contrôle d’accès dans Azure Data Lake Storage Gen2 (préversion)
description: Utilisez l’interface de ligne de commande Azure pour gérer les répertoires et les listes de contrôle d’accès (ACL, access-control list) de fichiers et de répertoires dans des comptes de stockage dotés d’un espace de noms hiérarchique.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: dcd75cfefd53b3c9104052146607869515e1c86e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534294"
---
# <a name="use-azure-cli-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Utiliser Azure CLI pour les fichiers et les listes de contrôle d’accès dans Azure Data Lake Storage Gen2 (préversion)

Cet article vous explique comment utiliser l’[interface de ligne de commande (CLI) Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) pour créer et gérer des répertoires, des fichiers et des autorisations dans des comptes de stockage dotés d’un espace de noms hiérarchique. 

> [!IMPORTANT]
> L’extension `storage-preview` qui est présentée dans cet article est actuellement une fonctionnalité d’évaluation publique.

[Exemple](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#adls-gen2-support) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2) | [Envoyer des commentaires](https://github.com/Azure/azure-cli-extensions/issues)
## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](data-lake-storage-quickstart-create-account.md) instructions.
> * Azure CLI version `2.0.67` ou ultérieure.

## <a name="install-the-storage-cli-extension"></a>Installer l’extension CLI de stockage

1. Ouvrez [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) ou, si vous avez [installé](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI localement, ouvrez une application console de commandes telle que Windows PowerShell.

2. Vérifiez que la version `2.0.67` d’Azure CLI ou une version supérieure est installée à l’aide de la commande suivante.

   ```azurecli
    az --version
   ```
   Si la version d’Azure CLI est inférieure à `2.0.67`, installez une version plus récente. Voir [Installer l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

3. Installez l’extension `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

## <a name="connect-to-the-account"></a>Se connecter au compte

1. Si vous utilisez Azure CLI localement, exécutez la commande login.

   ```azurecli
   az login
   ```

   Si l’interface CLI peut ouvrir votre navigateur par défaut, elle le fait et charge une page de connexion Azure par la même occasion.

   Sinon, ouvrez une page de navigateur à l’adresse [https://aka.ms/devicelogin](https://aka.ms/devicelogin) et entrez le code d’autorisation affiché dans votre terminal. Connectez-vous ensuite avec les informations d’identification de votre compte dans le navigateur.

   Pour en savoir plus sur les différentes méthodes d’authentification, consultez Se connecter avec Azure CLI.

2. Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage qui doit héberger votre site web statique.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

## <a name="create-a-file-system"></a>Créer un système de fichiers

Un système de fichiers agit comme un conteneur pour vos fichiers. Vous pouvez en créer un à l’aide de la commande `az storage container create`. 

Cet exemple crée un système de fichiers nommé `my-file-system`.

```azurecli
az storage container create --name my-file-system
```

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire à l’aide de la commande `az storage blob directory create`. 

Cet exemple ajoute un répertoire nommé `my-directory` à un système de fichiers lui-même nommé `my-file-system` qui se trouve dans un compte appelé `mystorageaccount`.

```azurecli
az storage blob directory create -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="show-directory-properties"></a>Afficher les propriétés du répertoire

Vous pouvez imprimer les propriétés d’un répertoire dans la console à l’aide de la commande `az storage blob show`.

```azurecli
az storage blob directory show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire à l’aide de la commande `az storage blob directory move`.

Cet exemple renomme un répertoire du nom `my-directory` au nom `my-new-directory`.

```azurecli
az storage blob directory move -c my-file-system -d my-new-directory -s my-directory --account-name mystorageaccount
```

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire à l’aide de la commande `az storage blob directory delete`.

Cet exemple supprime un répertoire nommé `my-directory`. 

```azurecli
az storage blob directory delete -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="check-if-a-directory-exists"></a>Vérifier l’existence d’un répertoire

Déterminez si un répertoire spécifique existe dans le système de fichiers à l’aide de la commande `az storage blob directory exist`.

Cet exemple indique si un répertoire nommé `my-directory` existe dans le système de fichiers `my-file-system`. 

```azurecli
az storage blob directory exists -c my-file-system -d my-directory --account-name mystorageaccount 
```

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire

Téléchargez un fichier à partir d’un répertoire à l’aide de la commande `az storage blob directory download`.

Cet exemple télécharge un fichier nommé `upload.txt` à partir d’un répertoire nommé `my-directory`. 

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/upload.txt" -d "C:\mylocalfolder\download.txt"
```

Cet exemple télécharge l’intégralité d’un répertoire.

```azurecli
az storage blob directory download -c my-file-system --account-name mystorageaccount -s "my-directory/" -d "C:\mylocalfolder" --recursive
```

## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Affichez le contenu d’un répertoire à l’aide de la commande `az storage blob directory list`.

Cet exemple répertorie le contenu d’un répertoire nommé `my-directory` qui se trouve dans le système de fichiers `my-file-system` d’un compte de stockage appelé `mystorageaccount`. 

```azurecli
az storage blob directory list -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="upload-a-file-to-a-directory"></a>Télécharger un fichier dans un répertoire

Téléchargez un fichier dans un répertoire à l’aide de la commande `az storage blob directory upload`.

Cet exemple télécharge un fichier nommé `upload.txt` dans un répertoire nommé `my-directory`. 

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\upload.txt" -d my-directory
```

Cet exemple charge l’intégralité d’un répertoire.

```azurecli
az storage blob directory upload -c my-file-system --account-name mystorageaccount -s "C:\mylocaldirectory\" -d my-directory --recursive 
```

## <a name="show-file-properties"></a>Afficher les propriétés d’un fichier

Vous pouvez imprimer les propriétés d’un fichier dans la console à l’aide de la commande `az storage blob show`.

```azurecli
az storage blob show -c my-file-system -b my-file.txt --account-name mystorageaccount
```

## <a name="rename-or-move-a-file"></a>Renommer ou déplacer un fichier

Renommez ou déplacez un fichier à l’aide de la commande `az storage blob move`.

Cet exemple renomme un fichier du nom `my-file.txt` au nom `my-file-renamed.txt`.

```azurecli
az storage blob move -c my-file-system -d my-file-renamed.txt -s my-file.txt --account-name mystorageaccount
```

## <a name="delete-a-file"></a>Supprimer un fichier

Supprimez un fichier à l’aide de la commande `az storage blob delete`.

Cet exemple supprime un fichier nommé `my-file.txt`.

```azurecli
az storage blob delete -c my-file-system -b my-file.txt --account-name mystorageaccount 
```

## <a name="manage-permissions"></a>Gérer les autorisations

Vous pouvez récupérer, définir et mettre à jour les autorisations d’accès des répertoires et des fichiers.

### <a name="get-directory-and-file-permissions"></a>Obtenir des autorisations d’accès aux répertoires et aux fichiers

Obtenez l’ACL d’un **répertoire** à l’aide de la commande `az storage blob directory access show`.

Cet exemple obtient l’ACL d’un répertoire, puis imprime l’ACL sur la console.

```azurecli
az storage blob directory access show -d my-directory -c my-file-system --account-name mystorageaccount
```

Obtenez les autorisations d’accès d’un **fichier** à l’aide de la commande `az storage blob access show`. 

Cet exemple obtient l’ACL d’un fichier, puis imprime l’ACL sur la console.

```azurecli
az storage blob access show -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

L’image suivante montre la sortie après avoir obtenu l’ACL d’un répertoire.

![Obtenir la sortie ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Dans cet exemple, l’utilisateur propriétaire dispose des autorisations de lecture, d’écriture et d’exécution. Le groupe propriétaire dispose uniquement des autorisations de lecture et d’exécution. Pour plus d’informations sur ces listes de contrôle d’accès, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Définir des autorisations d’accès aux répertoires et aux fichiers

Utilisez la commande `az storage blob directory access set` pour définir l’ACL d’un **répertoire**. 

Cet exemple définit l’ACL d’un répertoire pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis imprime l’ACL sur la console.

```azurecli
az storage blob directory access set -a "user::rw-,group::rw-,other::-wx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Utilisez la commande `az storage blob access set` pour définir l’ACL d’un **fichier**. 

Cet exemple définit l’ACL d’un fichier pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis imprime l’ACL sur la console.

```azurecli
az storage blob access set -a "user::rw-,group::rw-,other::-wx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
L’image suivante montre la sortie après avoir défini l’ACL d’un fichier.

![Obtenir la sortie ACL](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Dans cet exemple, l’utilisateur propriétaire et le groupe propriétaire disposent uniquement des autorisations de lecture et d’écriture. Tous les autres utilisateurs disposent d’autorisations d’écriture et d’exécution. Pour plus d’informations sur ces listes de contrôle d’accès, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Mettre à jour des autorisations d’accès aux répertoires et aux fichiers

Une autre façon de définir cette autorisation consiste à utiliser la commande `az storage blob directory access update` ou `az storage blob access update`. 

Mettez à jour l’ACL d’un répertoire ou d’un fichier en définissant le paramètre `-permissions` sur la forme abrégée d’une ACL.

Cet exemple met à jour l’ACL d’un **répertoire**.

```azurecli
az storage blob directory access update --permissions "rwxrwxrwx" -d my-directory -c my-file-system --account-name mystorageaccount
```

Cet exemple met à jour l’ACL d’un **fichier**.

```azurecli
az storage blob access update --permissions "rwxrwxrwx" -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```

Vous pouvez également mettre à jour l’utilisateur et le groupe propriétaire d’un répertoire ou d’un fichier en définissant les paramètres `--owner` ou `group` sur l’ID d’entité ou le nom d’utilisateur principal (UPN) d’un utilisateur. 

Cet exemple modifie le propriétaire d’un répertoire. 

```azurecli
az storage blob directory access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -d my-directory -c my-file-system --account-name mystorageaccount
```

Cet exemple modifie le propriétaire d’un fichier. 

```azurecli
az storage blob access update --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -b my-directory/upload.txt -c my-file-system --account-name mystorageaccount
```
## <a name="manage-user-defined-metadata"></a>Gérer les métadonnées définies par l’utilisateur

Vous pouvez ajouter des métadonnées définies par l’utilisateur à un fichier ou à un répertoire à l’aide de la commande `az storage blob directory metadata update` avec une ou plusieurs paires nom-valeur.

Cet exemple ajoute des métadonnées définies par l’utilisateur pour un répertoire nommé `my-directory`.

```azurecli
az storage blob directory metadata update --metadata tag1=value1 tag2=value2 -c my-file-system -d my-directory --account-name mystorageaccount
```

Cet exemple montre toutes les métadonnées définies par l’utilisateur pour le répertoire nommé `my-directory`.

```azurecli
az storage blob directory metadata show -c my-file-system -d my-directory --account-name mystorageaccount
```

## <a name="see-also"></a>Voir aussi

* [Exemple](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview)
* [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-cli-extensions/tree/master/src/storage-preview#mapping-from-adls-gen1-to-adls-gen2)
* [Envoyer des commentaires](https://github.com/Azure/azure-cli-extensions/issues)
* [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Code source](https://github.com/Azure/azure-cli-extensions/tree/master/src)

