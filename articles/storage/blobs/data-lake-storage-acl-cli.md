---
title: Utiliser Azure CLI pour définir des listes de contrôle d’accès dans Azure Data Lake Storage Gen2
description: Utilisez Azure CLI pour gérer les listes de contrôle d’accès dans des comptes de stockage dotés d’un espace de noms hiérarchique.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563162"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utiliser Azure CLI pour gérer des listes de contrôle d’accès dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser l’[interface de ligne de commande Azure](/cli/azure/) pour récupérer, définir et mettre à jour les listes de contrôle d’accès de répertoires et de fichiers.

L’héritage des listes ACL est déjà disponible pour les nouveaux éléments enfants créés sous un répertoire parent. Toutefois, vous pouvez également ajouter, mettre à jour et supprimer des listes de contrôle d’accès de manière récursive au niveau des éléments enfants existants d’un répertoire parent sans avoir à apporter ces modifications individuellement à chaque élément enfant.

[Référence](/cli/azure/storage/fs/access) | [Exemples](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md) | [Envoyer des commentaires](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- Azure CLI version `2.14.0` ou ultérieure.

- Une des autorisations de sécurité suivantes :

  - Un [principal de sécurité](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (Azure AD) provisionné qui a reçu le rôle [Propriétaire des données Blob de stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur cible, du groupe de ressources parent ou de l’abonnement.  

  - Utilisateur propriétaire du conteneur ou du répertoire cible auquel vous envisagez d’appliquer les paramètres ACL. Pour définir des listes de contrôle d’accès de façon récursive, cela inclut tous les éléments enfants du conteneur ou du répertoire cible.
  
  - Clé du compte de stockage.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Vérifiez que la version correcte d’Azure CLI est installée.

1. Ouvrez [Azure Cloud Shell](../../cloud-shell/overview.md) ou, si vous avez [installé](/cli/azure/install-azure-cli) Azure CLI localement, ouvrez une application console de commandes telle que Windows PowerShell.

2. Vérifiez que la version `2.14.0` d’Azure CLI ou une version supérieure est installée à l’aide de la commande suivante.

   ```azurecli
    az --version
   ```

   Si la version d’Azure CLI est inférieure à `2.14.0`, installez une version plus récente. Voir [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli).

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

## <a name="get-acls"></a>Obtenir les listes de contrôle d’accès

Obtenez l’ACL d’un **répertoire** à l’aide de la commande `az storage fs access show`.

Cet exemple obtient l’ACL d’un répertoire, puis imprime l’ACL sur la console.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Obtenez les autorisations d’accès d’un **fichier** à l’aide de la commande `az storage fs access show`. 

Cet exemple obtient l’ACL d’un fichier, puis imprime l’ACL sur la console.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

L’image suivante montre la sortie après l’obtention de la liste ACL d’un répertoire.

![Obtenir la sortie de liste ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Dans cet exemple, l’utilisateur propriétaire dispose d’autorisations de lecture, d’écriture et d’exécution. Le groupe propriétaire dispose uniquement d’autorisations de lecture et d’exécution. Pour plus d’informations sur les listes de contrôle d’accès, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="set-acls"></a>Définir les listes de contrôle d’accès

Lorsque vous *définissez* une liste de contrôle d’accès (ACL), vous **remplacez** l’ensemble de l’ACL, y compris toutes ses entrées. Si vous souhaitez modifier le niveau d’autorisation d’un principal de sécurité ou ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter d’autres entrées existantes, vous devez *mettre à jour* l’ACL à la place. Pour mettre à jour une liste de contrôle d’accès au lieu de la remplacer, consultez la section [Mettre à jour une liste de contrôle d’accès](#update-acls) de cet article.  

Si vous choisissez de *définir* la liste de contrôle d’accès, vous devez ajouter une entrée pour l’utilisateur propriétaire, une entrée pour le groupe propriétaire et une entrée pour tous les autres utilisateurs. Pour en savoir plus sur l’utilisateur propriétaire, le groupe propriétaire et tous les autres utilisateurs, consultez [Utilisateurs et identités](data-lake-storage-access-control.md#users-and-identities).

Cette section vous montre comment :

- Définir une liste de contrôle d’accès
- Définir des listes de contrôle d’accès de façon récursive

### <a name="set-an-acl"></a>Définir une liste de contrôle d’accès

Utilisez la commande `az storage fs access set` pour définir l’ACL d’un **répertoire**. 

Cet exemple définit l’ACL d’un répertoire pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis imprime l’ACL sur la console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Cet exemple définit l’ACL *par défaut* d’un répertoire pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis imprime l’ACL sur la console.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Utilisez la commande `az storage fs access set` pour définir l’ACL d’un **fichier**. 

Cet exemple définit l’ACL d’un fichier pour l’utilisateur propriétaire, le groupe propriétaire ou d’autres utilisateurs, puis imprime l’ACL sur la console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Pour définir l’ACL d’un groupe ou d’un utilisateur spécifique, utilisez leurs ID d’objet respectifs. Par exemple, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

L’image suivante montre la sortie après la définition de la liste ACL d’un fichier.

![Obtenir la sortie de liste ACL 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Dans cet exemple, l’utilisateur propriétaire et le groupe propriétaire disposent uniquement des autorisations de lecture et d’écriture. Tous les autres utilisateurs disposent des autorisations d’écriture et d’exécution. Pour plus d’informations sur les listes de contrôle d’accès, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Définir des listes de contrôle d’accès de façon récursive

Définissez des listes de contrôle d’accès de manière récursive à l’aide de la commande [az storage fs access set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive).

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`. Ces entrées donnent à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donnent au groupe propriétaire uniquement des autorisations de lecture et d’exécution, et ne donnent à tous les autres aucun accès. La dernière entrée de la liste de contrôle d’accès dans cet exemple donne à un utilisateur spécifique avec l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx » des autorisations de lecture et d’exécution.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Si vous souhaitez définir une entre ACL **par défaut**, ajoutez le préfixe `default:` à chaque entrée. Par exemple, `default:user::rwx` ou `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Mettre à jour les ACL

Lorsque vous *mettez à jour* une liste de contrôle d’accès, vous modifiez l’ACL au lieu de la remplacer. Par exemple, vous pouvez ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter les autres principaux de sécurité listés dans l’ACL.  Pour remplacer la liste de contrôle d’accès au lieu de la mettre à jour, consultez la section [Mettre à jour une liste de contrôle d’accès](#set-acls) de cet article.

Pour mettre à jour une liste de contrôle d’accès, créez un nouvel objet ACL avec l’entrée de l’ACL à mettre à jour, puis utilisez cet objet dans l’opération de mise à jour de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à mettre à jour.

Cette section vous montre comment :

- Mettre à jour une ACL
- Mettre à jour les listes de contrôle d’accès de manière récursive

### <a name="update-an-acl"></a>Mettre à jour une ACL

Une autre façon de définir cette autorisation consiste à utiliser la commande `az storage fs access set`. 

Mettez à jour l’ACL d’un répertoire ou d’un fichier en définissant le paramètre `-permissions` sur la forme abrégée d’une ACL.

Cet exemple met à jour l’ACL d’un **répertoire**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Cet exemple met à jour l’ACL d’un **fichier**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Pour mettre à jour l’ACL d’un groupe ou d’un utilisateur spécifique, utilisez leurs ID d’objet respectifs. Par exemple, `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` ou `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

Vous pouvez également mettre à jour l’utilisateur et le groupe propriétaire d’un répertoire ou d’un fichier en définissant les paramètres `--owner` ou `group` sur l’ID d’entité ou le nom d’utilisateur principal (UPN) d’un utilisateur.

Cet exemple modifie le propriétaire d’un répertoire.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Cet exemple modifie le propriétaire d’un fichier. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>Mettre à jour les listes de contrôle d’accès de manière récursive

Mettez à jour des listes de contrôle d’accès de manière récursive à l’aide de la commande [az storage fs access update-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive).

Cet exemple met à jour une entrée de liste de contrôle d’accès avec l’autorisation d’écriture.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Si vous souhaitez mettre à jour une entrée ACL **par défaut**, ajoutez le préfixe `default:` à chaque entrée. Par exemple, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>Supprimer des entrées de liste de contrôle d’accès (ACL) de manière récursive

Vous pouvez supprimer une ou plusieurs entrées de liste de contrôle d’accès (ACL) de manière récursive. Pour supprimer une entrée de liste de contrôle d’accès, créez un nouvel objet ACL pour l’entrée ACL à supprimer, puis utilisez cet objet dans l’opération de suppression de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à supprimer.

Supprimez les entrées de liste de contrôle d’accès à l’aide de la commande [az storage fs access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive).

Cet exemple supprime une entrée de liste de contrôle d’accès dans le répertoire racine du conteneur.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Si vous souhaitez supprimer une entre ACL **par défaut**, ajoutez le préfixe `default:` à chaque entrée. Par exemple, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Récupérer suite à des échecs

Vous pouvez rencontrer des erreurs d’exécution ou d’autorisation lors de la modification récursive des listes de contrôle d’accès. Pour les erreurs d’exécution, redémarrez le processus à partir du début. Des erreurs d’autorisation peuvent se produire si le principal de sécurité ne dispose pas des autorisations suffisantes pour modifier la liste de contrôle d’accès d’un répertoire ou d’un fichier qui se trouve dans la hiérarchie de répertoires en cours de modification. Résolvez le problème d’autorisation, puis choisissez de reprendre le processus à partir du point de défaillance à l’aide d’un jeton de continuation ou de redémarrer le processus à partir du début. Vous n’êtes pas obligé d’utiliser le jeton de continuation si vous préférez redémarrer à partir du début. Vous pouvez réappliquer les entrées de liste de contrôle d’accès sans incidence négative.

En cas d’échec, vous pouvez retourner un jeton de continuation en affectant au paramètre `--continue-on-failure` la valeur `false`. Une fois que vous avez corrigé les erreurs, vous pouvez reprendre le processus à partir du point de défaillance en exécutant à nouveau la commande, puis en affectant le paramètre `--continuation` au jeton de continuation.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Si vous souhaitez que le processus se termine sans être interrompu par des erreurs d’autorisation, vous pouvez le spécifier.

Pour vous assurer que le processus se termine sans interruption, définissez le paramètre `--continue-on-failure` sur `true`.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Voir aussi

- [Exemples](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Envoyer des commentaires](https://github.com/Azure/azure-cli-extensions/issues)
- [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listes de contrôle d’accès (ACL) dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)