---
title: SDK Java Azure Data Lake Storage Gen2 pour les fichiers et les listes de contrôle d’accès
description: Utilisez des bibliothèques Stockage Azure pour Java pour gérer les répertoires et les listes de contrôle d’accès (ACL, access-control list) de fichiers et de répertoires dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé.
author: normesta
ms.service: storage
ms.date: 01/11/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1cc6954569c509c977634a8e1cdd52c5c55b2100
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108125"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Utiliser Java pour gérer les répertoires, les fichiers et les listes de contrôle d’accès dans Azure Data Lake Storage Gen2

Cet article vous explique comment utiliser Java pour créer et gérer des répertoires, des fichiers et des autorisations dans des comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé. 

[Package (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [Référence de l’API](/java/api/overview/azure/storage-file-datalake-readme) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prérequis

> [!div class="checklist"]
> * Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](../common/storage-account-create.md) instructions.

## <a name="set-up-your-project"></a>Configuration de votre projet

Pour commencer, ouvrez [cette page](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) et recherchez la dernière version de la bibliothèque Java. Ensuite, ouvrez le fichier *pom.xml* dans votre éditeur de texte. Ajoutez un élément de dépendance qui référence cette version.

Si vous envisagez d’authentifier votre application cliente avec Azure Active Directory (AD), ajoutez une dépendance à la bibliothèque de client de secrets Azure. Consultez [Adding the Secret Client Library package to your project](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Ajoutez ensuite ces instructions Imports à votre fichier de code.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Se connecter au compte 

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. 

### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte. 

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’une clé de compte.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Se connecter avec Azure Active Directory (Azure AD)

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) afin d’authentifier votre application auprès d’Azure AD.

Cet exemple crée une instance **DataLakeServiceClient** à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  Pour obtenir ces valeurs, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Pour plus d’exemples, consultez la documentation [Bibliothèque de client Azure Identity pour Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).


## <a name="create-a-container"></a>Créez un conteneur.

Un conteneur fait office de système de fichiers pour vos fichiers. Vous pouvez en créer un en appelant la méthode **DataLakeServiceClient.createFileSystem**.

Cet exemple crée un conteneur nommé `my-file-system`. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Créer un répertoire

Créez une référence de répertoire en appelant la méthode **DataLakeFileSystemClient.createDirectory**.

Cet exemple ajoute un répertoire nommé `my-directory` à un conteneur, puis ajoute un sous-répertoire nommé `my-subdirectory`. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire en appelant la méthode **DataLakeDirectoryClient.rename**. Transmettez un paramètre au chemin d’accès du répertoire souhaité. 

Cet exemple renomme un sous-répertoire avec le nom `my-subdirectory-renamed`.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

Cet exemple déplace un répertoire nommé `my-subdirectory-renamed` vers un sous-répertoire d’un répertoire nommé `my-directory-2`. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire en appelant la méthode **DataLakeDirectoryClient.deleteWithResponse**.

Cet exemple supprime un répertoire nommé `my-directory`.   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire

Tout d’abord, créez une référence de fichier dans le répertoire cible en créant une instance de la classe **DataLakeFileClient**. Téléchargez un fichier en appelant la méthode **DataLakeFileClient.append**. Veillez à effectuer le chargement en appelant la méthode **DataLakeFileClient.FlushAsync**.

Cet exemple charge un fichier texte dans un répertoire nommé `my-directory`.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Si la taille de votre fichier est importante, votre code devra effectuer plusieurs appels à la méthode **DataLakeFileClient.append**. Utilisez plutôt la méthode **DataLakeFileClient.uploadFromFile**. Vous pourrez ainsi charger la totalité du fichier en un seul appel. 
>
> Vous en trouverez un exemple dans la section suivante.

## <a name="upload-a-large-file-to-a-directory"></a>Charger un fichier volumineux dans un répertoire

Utilisez la méthode **DataLakeFileClient.uploadFromFile** pour charger des fichiers volumineux sans avoir à effectuer plusieurs appels à la méthode **DataLakeFileClient.append**.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire

Tout d’abord, créez une instance **DataLakeFileClient** qui représente le fichier que vous souhaitez télécharger. Utilisez la méthode **DataLakeFileClient.read** pour lire le fichier. Utilisez n’importe quelle API de traitement de fichiers .NET pour enregistrer les octets du flux de données dans un fichier. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Cet exemple affiche les noms de chaque fichier situé dans un répertoire nommé `my-directory`.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Gérer les listes de contrôle d’accès (ACL, access control list)

Vous pouvez obtenir, définir et mettre à jour les autorisations d’accès des répertoires et des fichiers.

> [!NOTE]
> Si vous utilisez Azure Active Directory (Azure AD) pour autoriser l’accès, assurez-vous que le [rôle Propriétaire des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) est attribué à votre principal de sécurité. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Gérer la liste ACL d’un répertoire

Cet exemple obtient puis définit l’ACL d’un répertoire nommé `my-directory`. Cet exemple donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture.

> [!NOTE]
> Si votre application autorise l’accès en utilisant Azure Active Directory (Azure AD), vérifiez que le [rôle de propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) a été attribué au principal de sécurité utilisé par votre application pour autoriser l’accès. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Vous pouvez également obtenir et définir la liste de contrôle d’accès du répertoire racine d’un conteneur. Pour obtenir le répertoire racine, transmettez une chaîne vide (`""`) dans la méthode **DataLakeFileSystemClient.getDirectoryClient**.

### <a name="manage-a-file-acl"></a>Gérer la liste ACL d’un fichier

Cet exemple obtient puis définit l’ACL d’un fichier nommé `upload-file.txt`. Cet exemple donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture.

> [!NOTE]
> Si votre application autorise l’accès en utilisant Azure Active Directory (Azure AD), vérifiez que le [rôle de propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) a été attribué au principal de sécurité utilisé par votre application pour autoriser l’accès. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-an-acl-recursively"></a>Définir une liste de contrôle d’accès (ACL) de manière récursive

Vous pouvez ajouter, mettre à jour et supprimer des listes ACL de manière récursive au niveau des éléments enfants existants d’un répertoire parent sans avoir à apporter ces modifications individuellement à chaque élément enfant. Pour plus d’informations, consultez [Définir des listes de contrôle d’accès (ACL) de manière récursive pour Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Voir aussi

* [Documentation de référence de l’API](/java/api/overview/azure/storage-file-datalake-readme)
* [Package (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)