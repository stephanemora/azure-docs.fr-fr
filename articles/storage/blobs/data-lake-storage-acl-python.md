---
title: Utiliser Python pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2
description: Utilisez Python pour gérer les listes de contrôle d’accès (ACL, Access Control List) dans les comptes de stockage dotés d’un espace de noms hiérarchique activé.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653687"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utiliser Python pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser Python pour récupérer, définir et mettre à jour les listes de contrôle d’accès des répertoires et des fichiers. 

L’héritage des listes ACL est déjà disponible pour les nouveaux éléments enfants créés sous un répertoire parent. Toutefois, vous pouvez également ajouter, mettre à jour et supprimer des listes de contrôle d’accès de manière récursive au niveau des éléments enfants existants d’un répertoire parent sans avoir à apporter ces modifications individuellement à chaque élément enfant. 

[Package (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/) | [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [Exemples de listes de contrôle d’accès récursives](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) | [Référence de l’API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- Azure CLI version `2.6.0` ou ultérieure.

- Une des autorisations de sécurité suivantes :

  - Un [principal de sécurité](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (AD) provisionné qui a reçu le rôle [Propriétaire des données Blob de stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur cible, du groupe de ressources parent ou de l’abonnement.  

  - Utilisateur propriétaire du conteneur ou du répertoire cible auquel vous envisagez d’appliquer les paramètres ACL. Pour définir des listes de contrôle d’accès de façon récursive, cela inclut tous les éléments enfants du conteneur ou du répertoire cible.
  
  - Clé du compte de stockage.

## <a name="set-up-your-project"></a>Configuration de votre projet

Installez la bibliothèque cliente Azure Data Lake Storage pour Python à l’aide de [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Ajoutez les instructions d’importation suivantes au début de votre fichier de code.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Se connecter au compte

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Se connecter avec Azure Active Directory (AD)

> [!NOTE]
> Si vous utilisez Azure Active Directory (Azure AD) pour autoriser l’accès, assurez-vous que le [rôle Propriétaire des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) est attribué à votre principal de sécurité. Pour en savoir plus sur l’application des autorisations de liste de contrôle d’accès et les conséquences de leur modification, consultez [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour Python](https://pypi.org/project/azure-identity/) pour authentifier votre application auprès d’Azure AD.

Obtenez un ID client, un secret client et un ID de locataire. Pour ce faire, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](../common/storage-auth-aad-app.md). Dans le cadre de ce processus, vous devrez attribuer l’un des rôles [Azure RBAC (contrôle d’accès en fonction du rôle Azure)](../../role-based-access-control/overview.md) suivants à votre principal de sécurité. 

|Role|Capacité de paramétrage ACL|
|--|--|
|[Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tous les répertoires et fichiers du compte.|
|[Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Seuls les répertoires et les fichiers appartenant au principal de sécurité.|

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Pour obtenir plus d’exemples, consultez la documentation [Bibliothèque de client Azure Identity pour Python](https://pypi.org/project/azure-identity/).

### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte.

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’une clé de compte.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Remplacez la valeur d’espace réservé `storage_account_name` par le nom de votre compte de stockage.

- Remplacez la valeur d’espace réservé `storage_account_key` par la clé d’accès de votre compte de stockage.

## <a name="set-acls"></a>Définir les listes de contrôle d’accès

Lorsque vous *définissez* une liste de contrôle d’accès (ACL), vous **remplacez** l’ensemble de l’ACL, y compris toutes ses entrées. Si vous souhaitez modifier le niveau d’autorisation d’un principal de sécurité ou ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter d’autres entrées existantes, vous devez *mettre à jour* l’ACL à la place. Pour mettre à jour une liste de contrôle d’accès au lieu de la remplacer, consultez la section [Mettre à jour une liste de contrôle d’accès](#update-acls-recursively) de cet article.  

Cette section vous montre comment :

- Définir la liste de contrôle d’accès d’un répertoire
- Définir la liste de contrôle d’accès d’un fichier

### <a name="set-the-acl-of-a-directory"></a>Définir la liste de contrôle d’accès d’un répertoire

Obtenez la liste de contrôle d’accès (ACL) d’un répertoire en appelant la méthode **DataLakeDirectoryClient.get_access_control** et définissez l’ACL en appelant la méthode **DataLakeDirectoryClient.set_access_control**.

Cet exemple obtient et définit la liste ACL d’un répertoire nommé `my-directory`. La chaîne `rwxr-xrw-` donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture et en écriture.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Vous pouvez également obtenir et définir la liste de contrôle d’accès du répertoire racine d’un conteneur. Pour récupérer le répertoire racine, appelez la méthode **FileSystemClient._get_root_directory_client**.

### <a name="set-the-acl-of-a-file"></a>Définir la liste de contrôle d’accès d’un fichier

Obtenez la liste de contrôle d’accès (ACL) d’un fichier en appelant la méthode **DataLakeFileClient.get_access_control** et définissez l’ACL en appelant la méthode **DataLakeFileClient.set_access_control**.

Cet exemple obtient et définit la liste ACL d’un fichier nommé `my-file.txt`. La chaîne `rwxr-xrw-` donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture et en écriture.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>Définir des listes de contrôle d’accès de façon récursive

Lorsque vous *définissez* une liste de contrôle d’accès (ACL), vous **remplacez** l’ensemble de l’ACL, y compris toutes ses entrées. Si vous souhaitez modifier le niveau d’autorisation d’un principal de sécurité ou ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter d’autres entrées existantes, vous devez *mettre à jour* l’ACL à la place. Pour mettre à jour une liste de contrôle d’accès au lieu de la remplacer, consultez la section [Mettre à jour les listes de contrôle d’accès de manière récursive](#update-acls-recursively) de cet article.

Définissez les listes de contrôle d’accès de manière récursive en appelant la méthode **DataLakeDirectoryClient.set_access_control_recursive**.

Si vous souhaitez définir une entrée de liste de contrôle d’accès **par défaut**, ajoutez la chaîne `default:` au début de chaque chaîne d’entrée de liste de contrôle d’accès.

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`.

Cette méthode accepte un paramètre booléen nommé `is_default_scope` qui spécifie s’il faut définir la liste de contrôle d’accès par défaut. Si la valeur de ce paramètre est `True`, les entrées de la liste de contrôle d'accès sont précédées de la chaîne `default:`.

Ces entrées de la liste de contrôle d'accès donnent à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution et donnent uniquement au groupe propriétaire des autorisations de lecture et d’exécution. Les autres groupes ne reçoivent aucun accès. La dernière entrée de la liste de contrôle d’accès de cet exemple donne à un utilisateur spécifique les autorisations de lecture et d’exécution de l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ». Ces entrées accordent aux utilisateurs propriétaires les autorisations de lecture, d’écriture et d’exécution, ce qui donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution. Les autres groupes ne reçoivent aucun accès. La dernière entrée de la liste de contrôle d’accès dans cet exemple donne à un utilisateur spécifique avec l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx » des autorisations de lecture et d’exécution.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Pour voir un exemple qui traite des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’[exemple](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) Python .

## <a name="update-acls-recursively"></a>Mettre à jour les listes de contrôle d’accès de manière récursive

Lorsque vous *mettez à jour* une liste de contrôle d’accès, vous modifiez l’ACL au lieu de la remplacer. Par exemple, vous pouvez ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter les autres principaux de sécurité listés dans l’ACL.  Pour remplacer la liste de contrôle d’accès au lieu de la mettre à jour, consultez la section [Mettre à jour une liste de contrôle d’accès](#set-acls) de cet article.

Pour mettre à jour une liste de contrôle d’accès de manière récursive, créez un objet ACL avec l’entrée ACL à mettre à jour, puis utilisez cet objet dans l’opération de mise à jour de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à mettre à jour. Mettez à jour une liste de contrôle d’accès de manière récursive en appelant la méthode **DataLakeDirectoryClient.update_access_control_recursive**. Si vous souhaitez mettre à jour une entrée de liste de contrôle d’accès **par défaut**, ajoutez la chaîne `default:` au début de chaque chaîne d’entrée de liste de contrôle d’accès.

Cet exemple met à jour une entrée de liste de contrôle d’accès avec l’autorisation d’écriture.

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`. Cette méthode accepte un paramètre booléen nommé `is_default_scope` qui spécifie s’il faut mettre à jour la liste de contrôle d’accès par défaut. Si la valeur de ce paramètre est `True`, l’entrée de la liste de contrôle d'accès mise à jour est précédée de la chaîne `default:`.  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Pour voir un exemple qui traite des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’[exemple](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) Python .

## <a name="remove-acl-entries-recursively"></a>Supprimer des entrées de liste de contrôle d’accès (ACL) de manière récursive

Vous pouvez supprimer une ou plusieurs entrées ACL. Pour supprimer des entrées ACL de manière récursive, créez un objet ACL pour l’entrée ACL à supprimer, puis utilisez cet objet dans l’opération de suppression de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à supprimer. 

Supprimez des entrées de liste de contrôle d’accès en appelant la méthode **DataLakeDirectoryClient.remove_access_control_recursive**. Si vous souhaitez supprimer une entrée de liste de contrôle d’accès **par défaut**, ajoutez la chaîne `default:` au début de la chaîne d’entrée de liste de contrôle d’accès. 

Cet exemple supprimer une entrée de liste de contrôle d’accès dans la liste ACL du répertoire nommé `my-parent-directory`. Cette méthode accepte un paramètre booléen nommé `is_default_scope` qui spécifie s’il faut supprimer l’entrée de la liste de contrôle d’accès par défaut. Si la valeur de ce paramètre est `True`, l’entrée de la liste de contrôle d'accès mise à jour est précédée de la chaîne `default:`. 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Pour voir un exemple qui traite des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’[exemple](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) Python .

## <a name="recover-from-failures"></a>Récupérer suite à des échecs

Vous pouvez rencontrer des erreurs d’exécution ou d’autorisation. Pour les erreurs d’exécution, redémarrez le processus à partir du début. Des erreurs d’autorisation peuvent se produire si le principal de sécurité ne dispose pas des autorisations suffisantes pour modifier la liste de contrôle d’accès d’un répertoire ou d’un fichier qui se trouve dans la hiérarchie de répertoires en cours de modification. Résolvez le problème d’autorisation, puis choisissez de reprendre le processus à partir du point de défaillance à l’aide d’un jeton de continuation ou de redémarrer le processus à partir du début. Vous n’êtes pas obligé d’utiliser le jeton de continuation si vous préférez redémarrer à partir du début. Vous pouvez réappliquer les entrées de liste de contrôle d’accès sans incidence négative.

Cet exemple retourne un jeton de continuation en cas d’échec. L’application peut appeler à nouveau cet exemple de méthode une fois que l’erreur a été traitée et transmettre le jeton de continuation. Si cet exemple de méthode est appelé pour la première fois, l’application peut transmettre une valeur de ``None`` pour le paramètre de jeton de continuation.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Pour voir un exemple qui traite des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’[exemple](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) Python .

Si vous souhaitez que le processus se termine sans être interrompu par des erreurs d’autorisation, vous pouvez le spécifier.

Pour vous assurer que le processus se termine sans interruption, ne passez pas de jeton de continuation dans la méthode **DataLakeDirectoryClient.set_access_control_recursive**.

Cet exemple définit les entrées ACL de manière récursive. Si ce code rencontre une erreur d’autorisation, il enregistre cet échec et poursuit l’exécution. Cet exemple inscrit le nombre d’échecs sur la console.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Pour voir un exemple qui traite des ACL de manière récursive dans des lots en spécifiant une taille de lot, consultez l’[exemple](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py) Python .

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Voir aussi

- [Documentation de référence de l’API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Package (Python Package Index)](https://pypi.org/project/azure-storage-file-datalake/)
- [Exemples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-python/issues)
- [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listes de contrôle d’accès (ACL) dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)