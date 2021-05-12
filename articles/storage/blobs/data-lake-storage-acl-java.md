---
title: Utiliser Java pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2
description: Utilisez les bibliothèques Stockage Azure pour Java qui permettent de gérer les listes de contrôle d’accès (ACL, Access-Control List) dans les comptes de stockage dotés d’un espace de noms hiérarchique (HNS) activé.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 75f65c87d47232aa0809475d38a82ffe68203df6
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109633696"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utiliser Java pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser Java pour récupérer, définir et mettre à jour les listes de contrôle d’accès des répertoires et des fichiers. 

L’héritage des listes ACL est déjà disponible pour les nouveaux éléments enfants créés sous un répertoire parent. Toutefois, vous pouvez également ajouter, mettre à jour et supprimer des listes de contrôle d’accès de manière récursive au niveau des éléments enfants existants d’un répertoire parent sans avoir à apporter ces modifications individuellement à chaque élément enfant. 

[Package (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [Référence de l’API](/java/api/overview/azure/storage-file-datalake-readme) | [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- Azure CLI version `2.6.0` ou ultérieure.

- Une des autorisations de sécurité suivantes :

  - Un [principal de sécurité](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (AD) provisionné qui a reçu le rôle [Propriétaire des données Blob de stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur cible, du groupe de ressources parent ou de l’abonnement.  

  - Utilisateur propriétaire du conteneur ou du répertoire cible auquel vous envisagez d’appliquer les paramètres ACL. Pour définir des listes de contrôle d’accès de façon récursive, cela inclut tous les éléments enfants du conteneur ou du répertoire cible.
  
  - Clé du compte de stockage.

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Se connecter avec Azure Active Directory (Azure AD)

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) afin d’authentifier votre application auprès d’Azure AD.

Obtenez un ID client, un secret client et un ID de locataire. Pour ce faire, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](../common/storage-auth-aad-app.md). Dans le cadre de ce processus, vous devrez attribuer l’un des rôles [Azure RBAC (contrôle d’accès en fonction du rôle Azure)](../../role-based-access-control/overview.md) suivants à votre principal de sécurité. 

|Role|Capacité de paramétrage ACL|
|--|--|
|[Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tous les répertoires et fichiers du compte.|
|[Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Seuls les répertoires et les fichiers appartenant au principal de sécurité.|

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Pour plus d’exemples, consultez la documentation [Bibliothèque de client Azure Identity pour Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity).

### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte. 

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’une clé de compte.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Définir les listes de contrôle d’accès

Lorsque vous *définissez* une liste de contrôle d’accès (ACL), vous **remplacez** l’ensemble de l’ACL, y compris toutes ses entrées. Si vous souhaitez modifier le niveau d’autorisation d’un principal de sécurité ou ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter d’autres entrées existantes, vous devez *mettre à jour* l’ACL à la place. Pour mettre à jour une liste de contrôle d’accès au lieu de la remplacer, consultez la section [Mettre à jour une liste de contrôle d’accès](#update-acls) de cet article.  

Si vous choisissez de *définir* la liste de contrôle d’accès, vous devez ajouter une entrée pour l’utilisateur propriétaire, une entrée pour le groupe propriétaire et une entrée pour tous les autres utilisateurs. Pour en savoir plus sur l’utilisateur propriétaire, le groupe propriétaire et tous les autres utilisateurs, consultez [Utilisateurs et identités](data-lake-storage-access-control.md#users-and-identities).

Cette section vous montre comment :

- Définir la liste de contrôle d’accès d’un répertoire
- Définir la liste de contrôle d’accès d’un fichier
- Définir des listes de contrôle d’accès de façon récursive 

### <a name="set-the-acl-of-a-directory"></a>Définir la liste de contrôle d’accès d’un répertoire

Cet exemple obtient puis définit l’ACL d’un répertoire nommé `my-directory`. Cet exemple donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Vous pouvez également obtenir et définir la liste de contrôle d’accès du répertoire racine d’un conteneur. Pour obtenir le répertoire racine, transmettez une chaîne vide (`""`) dans la méthode **DataLakeFileSystemClient.getDirectoryClient**.

### <a name="set-the-acl-of-a-file"></a>Définir la liste de contrôle d’accès d’un fichier

Cet exemple obtient puis définit l’ACL d’un fichier nommé `upload-file.txt`. Cet exemple donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>Définir des listes de contrôle d’accès de façon récursive

Définissez les listes de contrôle d’accès de manière récursive en appelant la méthode **DataLakeDirectoryClient.setAccessControlRecursive**. Transmettez à cette méthode une [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) d’objets [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Chaque [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) définit une entrée de liste de contrôle d’accès.

Si vous souhaitez définir une entrée de contrôle d’accès **par défaut**, vous pouvez appeler la méthode **setDefaultScope** de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) et y transmettre la valeur **true**. 

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`. Cette méthode accepte un paramètre booléen nommé `isDefaultScope` qui spécifie s’il faut définir la liste de contrôle d’accès par défaut. Ce paramètre est utilisé chaque fois que la méthode **setDefaultScope** de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) est appelée. Ces entrées de la liste de contrôle d'accès donnent à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution et donnent uniquement au groupe propriétaire des autorisations de lecture et d’exécution. Les autres groupes ne reçoivent aucun accès. La dernière entrée de la liste de contrôle d’accès dans cet exemple donne à un utilisateur spécifique avec l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx » des autorisations de lecture et d’exécution.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Mettre à jour les ACL

Lorsque vous *mettez à jour* une liste de contrôle d’accès, vous modifiez l’ACL au lieu de la remplacer. Par exemple, vous pouvez ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter les autres principaux de sécurité listés dans l’ACL.  Pour remplacer la liste de contrôle d’accès au lieu de la mettre à jour, consultez la section [Mettre à jour une liste de contrôle d’accès](#set-acls) de cet article.

Cette section vous montre comment :

- Mettre à jour une ACL
- Mettre à jour les listes de contrôle d’accès de manière récursive

### <a name="update-an-acl"></a>Mettre à jour une ACL

Tout d’abord, récupérez la liste de contrôle d’accès d’un répertoire en appelant la méthode [PathAccessControl.getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist). Copiez la liste des entrées ACL dans un nouvel objet **List** de type [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Recherchez ensuite l’entrée que vous souhaitez mettre à jour et remplacez-la dans la liste. Définissez la liste de contrôle d’accès en appelant la méthode [DataLakeDirectoryClient.setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist).

Cet exemple met à jour la liste de contrôle d’accès d’un répertoire nommé `my-parent-directory` en remplaçant l’entrée pour tous les autres utilisateurs. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Vous pouvez également obtenir et définir la liste de contrôle d’accès du répertoire racine d’un conteneur. Pour obtenir le répertoire racine, transmettez une chaîne vide (`""`) dans la méthode **DataLakeFileSystemClient.getDirectoryClient**.

### <a name="update-acls-recursively"></a>Mettre à jour les listes de contrôle d’accès de manière récursive

Pour mettre à jour une liste de contrôle d’accès de manière récursive, créez un objet ACL avec l’entrée ACL à mettre à jour, puis utilisez cet objet dans l’opération de mise à jour de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à mettre à jour.

Mettez à jour des listes de contrôle d’accès de manière récursive en appelant la méthode **DataLakeDirectoryClient.updateAccessControlRecursive**.  Transmettez à cette méthode une [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) d’objets [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Chaque [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) définit une entrée de liste de contrôle d’accès. 

Si vous souhaitez mettre à jour une entrée de contrôle d’accès **par défaut**, vous pouvez appeler la méthode **setDefaultScope** de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) et y transmettre la valeur **true**. 

Cet exemple met à jour une entrée de liste de contrôle d’accès avec l’autorisation d’écriture. Cette méthode accepte un paramètre booléen nommé `isDefaultScope` qui spécifie s’il faut mettre à jour la liste de contrôle d’accès par défaut. Ce paramètre est utilisé à chaque appel de la méthode **setDefaultScope** de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>Supprimer des entrées de liste de contrôle d’accès

Vous pouvez supprimer une ou plusieurs entrées ACL. Cette section vous montre comment :

- Supprimer une entrée de liste de contrôle d’accès
- Supprimer des entrées de liste de contrôle d’accès (ACL) de manière récursive

### <a name="remove-an-acl-entry"></a>Supprimer une entrée de liste de contrôle d’accès

Tout d’abord, récupérez la liste de contrôle d’accès d’un répertoire en appelant la méthode [PathAccessControl.getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist). Copiez la liste des entrées ACL dans un nouvel objet **List** de type [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Localisez ensuite l’entrée que vous souhaitez supprimer et appelez la méthode **Remove** de l’objet **List**. Définissez la liste de contrôle d’accès mise à jour en appelant la méthode [DataLakeDirectoryClient.setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Supprimer des entrées de liste de contrôle d’accès (ACL) de manière récursive

Pour supprimer des entrées ACL de manière récursive, créez un objet ACL pour l’entrée ACL à supprimer, puis utilisez cet objet dans l’opération de suppression de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à supprimer.

Supprimez des entrées de liste de contrôle d’accès en appelant la méthode **DataLakeDirectoryClient.removeAccessControlRecursive**. Transmettez à cette méthode une [Liste](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) d’objets [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Chaque [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) définit une entrée de liste de contrôle d’accès. 

Si vous souhaitez supprimer une entrée de contrôle d’accès **par défaut**, vous pouvez appeler la méthode **setDefaultScope** de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) et y transmettre la valeur **true**.  

Cet exemple supprimer une entrée de liste de contrôle d’accès dans la liste ACL du répertoire nommé `my-parent-directory`. Cette méthode accepte un paramètre booléen nommé `isDefaultScope` qui spécifie s’il faut supprimer l’entrée de la liste de contrôle d’accès par défaut. Ce paramètre est utilisé à chaque appel de la méthode **setDefaultScope** de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Récupérer suite à des échecs

Vous pouvez rencontrer des erreurs d’exécution ou d’autorisation. Pour les erreurs d’exécution, redémarrez le processus à partir du début. Des erreurs d’autorisation peuvent se produire si le principal de sécurité ne dispose pas des autorisations suffisantes pour modifier la liste de contrôle d’accès d’un répertoire ou d’un fichier qui se trouve dans la hiérarchie de répertoires en cours de modification. Résolvez le problème d’autorisation, puis choisissez de reprendre le processus à partir du point de défaillance à l’aide d’un jeton de continuation ou de redémarrer le processus à partir du début. Vous n’êtes pas obligé d’utiliser le jeton de continuation si vous préférez redémarrer à partir du début. Vous pouvez réappliquer les entrées de liste de contrôle d’accès sans incidence négative.

Cet exemple retourne un jeton de continuation en cas d’échec. L’application peut appeler à nouveau cet exemple de méthode une fois que l’erreur a été traitée et transmettre le jeton de continuation. Si cet exemple de méthode est appelé pour la première fois, l’application peut transmettre une valeur de `null` pour le paramètre de jeton de continuation.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Si vous souhaitez que le processus se termine sans être interrompu par des erreurs d’autorisation, vous pouvez le spécifier.

Pour vous assurer que le processus se termine sans interruption, appelez la méthode **setContinueOnFailure** d’un objet [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) et passez la valeur **true**.

Cet exemple définit les entrées ACL de manière récursive. Si ce code rencontre une erreur d’autorisation, il enregistre cet échec et poursuit l’exécution. Cet exemple inscrit le nombre d’échecs sur la console.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Voir aussi

- [Documentation de référence de l’API](/java/api/overview/azure/storage-file-datalake-readme)
- [Package (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Exemples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Mappage de Gen1 à Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problèmes connus](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)
- [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listes de contrôle d’accès (ACL) dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)