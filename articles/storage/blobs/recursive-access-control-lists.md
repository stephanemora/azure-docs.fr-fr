---
title: Définir des listes ACL de manière récursive pour Azure Data Lake Storage Gen2 | Microsoft Docs
description: Vous pouvez ajouter, mettre à jour et supprimer des listes de contrôle d’accès (ACL) de manière récursive pour les éléments enfants existants d’un répertoire parent.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 10/07/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: cedb6d162829d63aaac1a36b35abee1faeae3f1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843394"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Définir des listes de contrôle d’accès (ACL) pour Azure Data Lake Storage Gen2

L’héritage des listes ACL est déjà disponible pour les nouveaux éléments enfants créés sous un répertoire parent. Maintenant, vous pouvez également ajouter, mettre à jour et supprimer des listes ACL de manière récursive pour les éléments enfants existants d’un répertoire parent sans avoir à apporter ces modifications individuellement à chaque élément enfant.

> [!NOTE]
> La possibilité de définir des listes ACL de manière récursive est en préversion publique et disponible dans toutes les régions.  

[Bibliothèques](#libraries) | [Exemples](#code-samples) | [Meilleures pratiques](#best-practice-guidelines) | [Envoyer des commentaires](#provide-feedback)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](data-lake-storage-quickstart-create-account.md) instructions.

- Autorisations appropriées pour exécuter le processus ACL récursif. L’autorisation correcte comprend l’un des éléments suivants : 

  - Un [principal de sécurité](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) Azure Active Directory (AD) provisionné qui a reçu le rôle [Propriétaire des données Blob de stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur cible, du groupe de ressources parent ou de l’abonnement.   

  - Utilisateur propriétaire du conteneur ou du répertoire cible auquel vous envisagez d’appliquer le processus ACL récursif. Cela comprend tous les éléments enfants du conteneur ou du répertoire cible. 

- Comprendre comment les listes ACL sont appliquées aux répertoires et aux fichiers. Consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

Pour afficher les instructions d’installation pour PowerShell, le SDK .NET et le SDK Python, consultez la section **Configurer votre projet** de cet article.

## <a name="set-up-your-project"></a>Configuration de votre projet

Installez les bibliothèques nécessaires.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vérifiez que le .NET Framework est installé. Consultez [Télécharger .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
2. Vérifiez que la version de PowerShell installée est `5.1` ou une version ultérieure à l’aide de la commande suivante.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Pour mettre à niveau votre version de PowerShell, consultez [Mise à niveau des instances Windows PowerShell existantes](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell).
    
3. Installez la dernière version du module PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

4. Fermez, puis rouvrez la console PowerShell.

5. Installez le module **Az.Storage** en préversion.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Pour plus d’informations sur l’installation des modules PowerShell, consultez [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Ouvrez une fenêtre de commande (par exemple : Windows PowerShell).

2. À partir de votre répertoire de projet, installez le package de préversion Azure.Storage.Files.DataLake à l’aide de la commande `dotnet add package`.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Ajoutez ces instructions using au début de votre fichier de code.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="python"></a>[Python](#tab/python)

1. Téléchargez la [bibliothèque cliente Azure Data Lake Storage pour Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0).

2. Installez la bibliothèque que vous avez téléchargée à l’aide de [pip](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

Ajoutez les instructions d’importation suivantes au début de votre fichier de code.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

---

## <a name="connect-to-your-account"></a>Se connecter à votre compte

Vous pouvez vous connecter à l’aide d’Azure Active Directory (AD) ou à l’aide d’une clé de compte. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ouvrez une fenêtre de commande Windows PowerShell, puis connectez-vous à votre abonnement Azure avec la commande `Connect-AzAccount` et suivez les instructions à l’écran.

```powershell
Connect-AzAccount
```

Si votre identité est associée à plusieurs abonnements, définissez comme abonnement actif l’abonnement du compte de stockage dans lequel vous souhaitez créer et gérer des répertoires. Dans cet exemple, remplacez la valeur d’espace réservé `<subscription-id>` par l’ID de votre abonnement.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Ensuite, choisissez la façon dont vous souhaitez que vos commandes obtiennent l’autorisation pour le compte de stockage. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Option 1 : Obtenir l’autorisation à l’aide d’Azure Active Directory (AD)

Avec cette approche, le système garantit que votre compte d’utilisateur dispose des autorisations de contrôle d’accès en fonction du rôle Azure (Azure RBAC) appropriées et des autorisations de liste de contrôle d’accès (ACL). 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

Le tableau suivant présente chacun des rôles pris en charge et leurs capacités en termes de paramétrage ACL.

|Role|Capacité de paramétrage ACL|
|--|--|
|[Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tous les répertoires et fichiers du compte.|
|[Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Seuls les répertoires et les fichiers appartenant au principal de sécurité.|

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Option n°2 : Obtenir l’autorisation à l’aide de la clé de compte de stockage

Avec cette approche, le système ne vérifie pas les autorisations Azure RBAC ou ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

### <a name="net"></a>[.NET](#tab/dotnet)

Pour utiliser les extraits de code de cet article, vous devez créer une instance [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) qui représente le compte de stockage.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Connexion avec Azure Active Directory (AD)

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) pour authentifier votre application auprès d’Azure AD.

Après avoir installé le package, ajoutez cette instruction using au début de votre fichier de code.

```csharp
using Azure.Identity;
```

Obtenez un ID client, un secret client et un ID de locataire. Pour ce faire, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](../common/storage-auth-aad-app.md). Dans le cadre de ce processus, vous devrez attribuer l’un des rôles [Azure RBAC (contrôle d’accès en fonction du rôle Azure)](../../role-based-access-control/overview.md) suivants à votre principal de sécurité. 

|Role|Capacité de paramétrage ACL|
|--|--|
|[Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tous les répertoires et fichiers du compte.|
|[Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Seuls les répertoires et les fichiers appartenant au principal de sécurité.|

Cet exemple crée une instance de [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

``` 

#### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte. 

Cet exemple crée une instance de [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) à l’aide d’une clé de compte.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

> [!NOTE]
> Pour plus d’exemples, consultez la documentation [Bibliothèque cliente Azure Identity pour .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

### <a name="python"></a>[Python](#tab/python)

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Se connecter avec Azure Active Directory (AD)

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour Python](https://pypi.org/project/azure-identity/) pour authentifier votre application auprès d’Azure AD.

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  Pour obtenir ces valeurs, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](../common/storage-auth-aad-app.md). Dans le cadre de ce processus, vous devrez attribuer l’un des rôles [Azure RBAC (contrôle d’accès en fonction du rôle Azure)](../../role-based-access-control/overview.md) suivants à votre principal de sécurité. 

|Role|Capacité de paramétrage ACL|
|--|--|
|[Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tous les répertoires et fichiers du compte.|
|[Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Seuls les répertoires et les fichiers appartenant au principal de sécurité.|

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Pour obtenir plus d’exemples, consultez la documentation [Bibliothèque de client Azure Identity pour Python](https://pypi.org/project/azure-identity/).

### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte. 

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’une clé de compte.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Remplacez la valeur d’espace réservé `storage_account_name` par le nom de votre compte de stockage.

- Remplacez la valeur d’espace réservé `storage_account_key` par la clé d’accès de votre compte de stockage.

---

## <a name="set-an-acl-recursively"></a>Définir une liste de contrôle d’accès (ACL) de manière récursive

Lorsque vous *définissez* une liste de contrôle d’accès (ACL), vous **remplacez** l’ensemble de l’ACL, y compris toutes ses entrées. Si vous souhaitez modifier le niveau d’autorisation d’un principal de sécurité ou ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter d’autres entrées existantes, vous devez *mettre à jour* l’ACL à la place. Pour mettre à jour une liste de contrôle d’accès au lieu de la remplacer, consultez la section [Mettre à jour une liste de contrôle d’accès (ACL) de manière récursive](#update-an-acl-recursively) de cet article.   

Cette section contient des exemples de définition d’une liste de contrôle d’accès (ACL) 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Définissez une liste de contrôle d’accès de manière récursive à l’aide de la cmdlet `Set-AzDataLakeGen2AclRecursive`.

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`. Ces entrées donnent à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donnent au groupe propriétaire uniquement des autorisations de lecture et d’exécution, et ne donnent à tous les autres aucun accès. La dernière entrée de la liste de contrôle d’accès dans cet exemple donne à un utilisateur spécifique avec l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx » des autorisations de lecture et d’exécution.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Si vous souhaitez définir une entrée de liste de contrôle d’accès **par défaut**, utilisez le paramètre **-DefaultScope** quand vous exécutez la commande **Set-AzDataLakeGen2ItemAclObject**. Par exemple : `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

### <a name="net"></a>[.NET](#tab/dotnet)

Définissez une liste de contrôle d’accès de manière récursive en appelant la méthode **DataLakeDirectoryClient.SetAccessControlRecursiveAsync**. Transmettez à cette méthode une [Liste](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Chaque [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) définit une entrée de liste de contrôle d’accès. 

Si vous souhaitez définir une entrée de liste de contrôle d’accès **par défaut**, vous pouvez définir la propriété [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) sur **true**. 

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`. Cette méthode accepte un paramètre booléen nommé `isDefaultScope` qui spécifie s’il faut définir la liste de contrôle d’accès par défaut. Ce paramètre est utilisé dans le constructeur de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Ces entrées de la liste de contrôle d'accès donnent à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donnent au groupe propriétaire uniquement des autorisations de lecture et d’exécution, et ne donnent à tous les autres aucun accès. La dernière entrée de la liste de contrôle d’accès dans cet exemple donne à un utilisateur spécifique avec l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx » des autorisations de lecture et d’exécution.

```cs
public async void SetACLRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlList = 
        new List<PathAccessControlItem>() 
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Group, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope),
                    
        new PathAccessControlItem(AccessControlType.Other, 
            RolePermissions.None, isDefaultScope),

        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read | 
            RolePermissions.Execute, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.SetAccessControlRecursiveAsync
        (accessControlList, null);
}

```

### <a name="python"></a>[Python](#tab/python)

Définissez une liste de contrôle d’accès de manière récursive en appelant la méthode **DataLakeDirectoryClient.set_access_control_recursive**.

Si vous souhaitez définir une entrée de liste de contrôle d’accès **par défaut**, ajoutez la chaîne `default:` au début de chaque chaîne d’entrée de liste de contrôle d’accès. 

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`. 

Cette méthode accepte un paramètre booléen nommé `is_default_scope` qui spécifie s’il faut définir la liste de contrôle d’accès par défaut. Si la valeur de ce paramètre est `True`, les entrées de la liste de contrôle d'accès sont précédées de la chaîne `default:`. 

Ces entrées de la liste de contrôle d'accès donnent à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution et donnent uniquement au groupe propriétaire des autorisations de lecture et d’exécution. Les autres groupes ne reçoivent aucun accès. La dernière entrée de la liste de contrôle d’accès de cet exemple donne à un utilisateur spécifique les autorisations de lecture et d’exécution de l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ». Ces entrées accordent aux utilisateurs propriétaires les autorisations de lecture, d’écriture et d’exécution, ce qui donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution. Les autres groupes ne reçoivent aucun accès. La dernière entrée de la liste de contrôle d’accès dans cet exemple donne à un utilisateur spécifique avec l’ID d’objet « xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx » des autorisations de lecture et d’exécution.

```python
def set_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:4a9028cf-f779-4032-b09d-970ebe3db258:r--'   

        if is_default_scope:
           acl = 'default:user::rwx,default:group::rwx,default:other::rwx,default:user:4a9028cf-f779-4032-b09d-970ebe3db258:r--'

        directory_client.set_access_control_recursive(acl=acl)
        
    except Exception as e:
     print(e)
```

---

## <a name="update-an-acl-recursively"></a>Mettre à jour une liste de contrôle d’accès (ACL) de manière récursive

Lorsque vous *mettez à jour* une liste de contrôle d’accès, vous modifiez l’ACL au lieu de la remplacer. Par exemple, vous pouvez ajouter un nouveau principal de sécurité à la liste de contrôle d’accès sans affecter les autres principaux de sécurité listés dans l’ACL.  Pour remplacez la liste de contrôle d’accès au lieu de la mettre à jour, consultez la section [Mettre à jour une liste de contrôle d’accès (ACL) de manière récursive](#set-an-acl-recursively) de cet article. 

Pour mettre à jour une liste de contrôle d’accès, créez un nouvel objet ACL avec l’entrée de l’ACL à mettre à jour, puis utilisez cet objet dans l’opération de mise à jour de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à mettre à jour.

Cette section contient des exemples de mise à jour d’une liste de contrôle d’accès (ACL)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Mettez à jour une liste de contrôle d’accès de manière récursive à l’aide de la cmdlet **Update-AzDataLakeGen2AclRecursive**. 

Cet exemple met à jour une entrée de liste de contrôle d’accès avec l’autorisation d’écriture. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Si vous souhaitez mettre à jour une entrée de liste de contrôle d’accès **par défaut**, utilisez le paramètre **-DefaultScope** lors de l’exécution de la commande **Set-AzDataLakeGen2ItemAclObject**. Par exemple : `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

### <a name="net"></a>[.NET](#tab/dotnet)

Mettez à jour une liste de contrôle d’accès de manière récursive en appelant la méthode **DataLakeDirectoryClient.UpdateAccessControlRecursiveAsync**.  Transmettez à cette méthode une [Liste](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Chaque [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) définit une entrée de liste de contrôle d’accès. 

Si vous souhaitez mettre à jour une entrée de liste de contrôle d’accès **par défaut**, vous pouvez définir la propriété [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) sur **true**. 

Cet exemple met à jour une entrée de liste de contrôle d’accès avec l’autorisation d’écriture. Cette méthode accepte un paramètre booléen nommé `isDefaultScope` qui spécifie s’il faut mettre à jour la liste de contrôle d’accès par défaut. Ce paramètre est utilisé dans le constructeur de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void UpdateACLsRecursively(DataLakeServiceClient serviceClient, bool isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
        GetDirectoryClient("my-parent-directory");

    List<PathAccessControlItem> accessControlListUpdate = 
        new List<PathAccessControlItem>()
    {
        new PathAccessControlItem(AccessControlType.User, 
            RolePermissions.Read |
            RolePermissions.Write | 
            RolePermissions.Execute, isDefaultScope, 
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
    };

    await directoryClient.UpdateAccessControlRecursiveAsync
        (accessControlListUpdate, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Mettez à jour une liste de contrôle d’accès de manière récursive en appelant la méthode **DataLakeDirectoryClient.update_access_control_recursive**. Si vous souhaitez mettre à jour une entrée de liste de contrôle d’accès **par défaut**, ajoutez la chaîne `default:` au début de chaque chaîne d’entrée de liste de contrôle d’accès. 

Cet exemple met à jour une entrée de liste de contrôle d’accès avec l’autorisation d’écriture.

Cet exemple définit la liste ACL d’un répertoire nommé `my-parent-directory`. Cette méthode accepte un paramètre booléen nommé `is_default_scope` qui spécifie s’il faut mettre à jour la liste de contrôle d’accès par défaut. Si la valeur de ce paramètre est `True`, l’entrée de la liste de contrôle d'accès mise à jour est précédée de la chaîne `default:`.  

```python
def update_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")

        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'   

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx'

        directory_client.update_access_control_recursive(acl=acl)

        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e)
```

---

## <a name="remove-acl-entries-recursively"></a>Supprimer des entrées de liste de contrôle d’accès (ACL) de manière récursive

Vous pouvez supprimer une ou plusieurs entrées de liste de contrôle d’accès (ACL) de manière récursive. Pour supprimer une entrée de liste de contrôle d’accès, créez un nouvel objet ACL pour l’entrée ACL à supprimer, puis utilisez cet objet dans l’opération de suppression de l’ACL. Ne récupérez pas la liste de contrôle d’accès existante, il vous suffit de fournir les entrées ACL à supprimer. 

Cette section contient des exemples de suppression d’une liste de contrôle d’accès (ACL). 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Supprimez des entrées de liste de contrôle d’accès à l’aide de la cmdlet **Remove-AzDataLakeGen2AclRecursive**. 

Cet exemple supprime une entrée de liste de contrôle d’accès dans le répertoire racine du conteneur.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Si vous souhaitez supprimer une entrée de liste de contrôle d’accès **par défaut**, utilisez le paramètre **-DefaultScope** lors de l’exécution de la commande **Set-AzDataLakeGen2ItemAclObject**. Par exemple : `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

### <a name="net"></a>[.NET](#tab/dotnet)

Supprimez des entrées de liste de contrôle d’accès en appelant la méthode **DataLakeDirectoryClient.RemoveAccessControlRecursiveAsync**. Transmettez à cette méthode une [Liste](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Chaque [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) définit une entrée de liste de contrôle d’accès. 

Si vous souhaitez supprimer une entrée de liste de contrôle d’accès **par défaut**, vous pouvez définir la propriété [PathAccessControlItem.DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) sur **true**. 

Cet exemple supprimer une entrée de liste de contrôle d’accès dans la liste ACL du répertoire nommé `my-parent-directory`. Cette méthode accepte un paramètre booléen nommé `isDefaultScope` qui spécifie s’il faut supprimer l’entrée de la liste de contrôle d’accès par défaut. Ce paramètre est utilisé dans le constructeur de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

```cs
public async void RemoveACLsRecursively(DataLakeServiceClient serviceClient, isDefaultScope)
{
    DataLakeDirectoryClient directoryClient =
        serviceClient.GetFileSystemClient("my-container").
            GetDirectoryClient("my-parent-directory");

    List<RemovePathAccessControlItem> accessControlListForRemoval = 
        new List<RemovePathAccessControlItem>()
        {
            new RemovePathAccessControlItem(AccessControlType.User, isDefaultScope,
            entityId: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"),
        };

    await directoryClient.RemoveAccessControlRecursiveAsync
        (accessControlListForRemoval, null);

}
```

### <a name="python"></a>[Python](#tab/python)

Supprimez des entrées de liste de contrôle d’accès en appelant la méthode **DataLakeDirectoryClient.remove_access_control_recursive**. Si vous souhaitez supprimer une entrée de liste de contrôle d’accès **par défaut**, ajoutez la chaîne `default:` au début de la chaîne d’entrée de liste de contrôle d’accès. 

Cet exemple supprimer une entrée de liste de contrôle d’accès dans la liste ACL du répertoire nommé `my-parent-directory`. Cette méthode accepte un paramètre booléen nommé `is_default_scope` qui spécifie s’il faut supprimer l’entrée de la liste de contrôle d’accès par défaut. Si la valeur de ce paramètre est `True`, l’entrée de la liste de contrôle d'accès mise à jour est précédée de la chaîne `default:`. 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:4a9028cf-f779-4032-b09d-970ebe3db258'

        if is_default_scope:
           acl = 'default:user:4a9028cf-f779-4032-b09d-970ebe3db258'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

---

## <a name="recover-from-failures"></a>Récupérer suite à des échecs

Vous pouvez rencontrer des erreurs d’exécution ou d’autorisation. Pour les erreurs d’exécution, redémarrez le processus à partir du début. Des erreurs d’autorisation peuvent se produire si le principal de sécurité ne dispose pas des autorisations suffisantes pour modifier la liste de contrôle d’accès d’un répertoire ou d’un fichier qui se trouve dans la hiérarchie de répertoires en cours de modification. Résolvez le problème d’autorisation, puis choisissez de reprendre le processus à partir du point de défaillance à l’aide d’un jeton de continuation ou de redémarrer le processus à partir du début. Vous n’êtes pas obligé d’utiliser le jeton de continuation si vous préférez redémarrer à partir du début. Vous pouvez réappliquer les entrées de liste de contrôle d’accès sans incidence négative.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Retournez les résultats à la variable. Acheminez les entrées en échec vers un tableau mis en forme.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

En fonction de la sortie du tableau, vous pouvez corriger les erreurs d’autorisation, puis reprendre l’exécution à l’aide du jeton de continuation.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

## <a name="net"></a>[.NET](#tab/dotnet)

Cet exemple retourne un jeton de continuation en cas d’échec. L’application peut appeler à nouveau cet exemple de méthode une fois que l’erreur a été traitée et transmettre le jeton de continuation. Si cet exemple de méthode est appelé pour la première fois, l’application peut transmettre une valeur de ``null`` pour le paramètre de jeton de continuation. 

```cs
public async Task<string> ResumeAsync(DataLakeServiceClient serviceClient,
    DataLakeDirectoryClient directoryClient,
    List<PathAccessControlItem> accessControlList, 
    string continuationToken)
{
    try
    {
        var accessControlChangeResult =
            await directoryClient.SetAccessControlRecursiveAsync(
                accessControlList, continuationToken: continuationToken, null);

        if (accessControlChangeResult.Value.Counters.FailedChangesCount > 0)
        {
            continuationToken =
                accessControlChangeResult.Value.ContinuationToken;
        }

        return continuationToken;
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
        return continuationToken;
    }

}
```

### <a name="python"></a>[Python](#tab/python)

Cet exemple retourne un jeton de continuation en cas d’échec. L’application peut appeler à nouveau cet exemple de méthode une fois que l’erreur a été traitée et transmettre le jeton de continuation. Si cet exemple de méthode est appelé pour la première fois, l’application peut transmettre une valeur de ``None`` pour le paramètre de jeton de continuation. 

```python
def resume_set_acl_recursive(continuation_token):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user::rwx,group::rwx,other::rwx,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x'

        acl_change_result = directory_client.set_access_control_recursive(acl=acl, continuation=continuation_token)

        continuation_token = acl_change_result.continuation

        return continuation_token
        
    except Exception as e:
     print(e) 
     return continuation_token
```

---

## <a name="resources"></a>Ressources

Cette section contient des liens vers des bibliothèques et des exemples de code.

#### <a name="libraries"></a>Bibliothèques

- [PowerShell](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fwww.powershellgallery.com%2Fpackages%2FAz.Storage%2F2.5.2-preview&data=02%7C01%7Cnormesta%40microsoft.com%7Ccdabce06132c42132b4008d849a2dfb1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637340311173215017&sdata=FWynO9UKTt7ESMCFgkWaL7J%2F%2BjODaRo5BD6G6yCx9os%3D&reserved=0)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Python](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2Fazure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A47%253A01Z%26se%3D2021-08-25T07%253A47%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DH1XYw4FTLJse%252BYQ%252BfamVL21UPVIKRnnh2mfudA%252BfI0I%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C95a5966d938a4902560e08d84912fe32%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339693209725909&sdata=acv4KWZdzkITw1lP0%2FiA3lZuW7NF5JObjY26IXttfGI%3D&reserved=0)

#### <a name="code-samples"></a>Exemples de code

- PowerShell : [Fichier Lisezmoi](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D) | [Exemple](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- NET : [Fichier Lisezmoi](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%252FSc2aDVW3De4A%252Fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0) | [Exemple](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python : [Fichier Lisezmoi](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0) | [Exemple](https://recursiveaclpr.blob.core.windows.net/privatedrop/datalake_samples_access_control_async.py?sv=2019-02-02&st=2020-08-24T07%3A48%3A10Z&se=2021-08-25T07%3A48%3A00Z&sr=b&sp=r&sig=%2F1c540%2BpXYyNcuTmWPWHg2m9SyClXLIMw7ChLZGsyD0%3D)

## <a name="best-practice-guidelines"></a>Conseils de bonnes pratiques

Cette section décrit certaines des meilleures pratiques pour définir des listes de contrôle d’accès (ACL) de manière récursive. 

#### <a name="handling-runtime-errors"></a>Traitement des erreurs d’exécution

Une erreur d’exécution peut se produire pour de nombreuses raisons (par exemple, une panne ou un problème de connectivité client). Si vous rencontrez une erreur d’exécution, redémarrez le processus ACL récursif. Les listes de contrôle d’accès peuvent être réappliquées à des éléments sans impact négatif. 

#### <a name="handling-permission-errors-403"></a>Traitement des erreurs d’autorisation (403)

Si vous rencontrez une exception de contrôle d’accès lors de l’exécution d’un processus ACL récursif, votre [principal de sécurité](https://docs.microsoft.com/azure/role-based-access-control/overview#security-principal) AD peut ne pas disposer des autorisations suffisantes pour appliquer une liste de contrôle d’accès à un ou plusieurs des éléments enfants dans la hiérarchie de répertoires. Lorsqu’une erreur d’autorisation se produit, le processus s’arrête et un jeton de continuation est fourni. Corrigez le problème d’autorisation, puis utilisez le jeton de continuation pour traiter le jeu de données restant. Les répertoires et les fichiers qui ont déjà été traités correctement n’ont pas besoin d’être retraités. Vous pouvez également choisir de redémarrer le processus ACL récursif. Les listes de contrôle d’accès peuvent être réappliquées à des éléments sans impact négatif. 

#### <a name="credentials"></a>Informations d'identification 

Nous vous recommandons de provisionner un principal de sécurité Azure AD auquel a été attribué le rôle [Propriétaire des données Blob de stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur ou du compte de stockage cible. 

#### <a name="performance"></a>Performances 

Pour réduire la latence, nous vous recommandons d’exécuter le processus ACL récursif dans une machine virtuelle Azure située dans la même région que votre compte de stockage. 

#### <a name="acl-limits"></a>Limites des listes de contrôle d’accès (ACL)

Le nombre maximal de listes ACL que vous pouvez appliquer à un répertoire ou à un fichier est de 32 ACL d’accès et de 32 ACL par défaut. Pour plus d’informations, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

<a id="provide-feedback"></a>

### <a name="provide-feedback-or-report-issues"></a>Envoyer des commentaires ou signaler des problèmes

Vous pouvez envoyer vos commentaires ou signaler un problème à l’adresse [recursiveACLfeedback@microsoft.com](mailto:recursiveACLfeedback@microsoft.com).

## <a name="see-also"></a>Voir aussi

- [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
- [Problèmes connus](data-lake-storage-known-issues.md)


