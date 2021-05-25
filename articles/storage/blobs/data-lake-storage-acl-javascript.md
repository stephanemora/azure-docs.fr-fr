---
title: Utiliser JavaScript (Node.js) pour gérer les listes ACL dans Azure Data Lake Storage Gen2
description: Utilisez la bibliothèque de client Azure Data Lake Storage pour JavaScript qui permet de gérer les listes de contrôle d’accès (ACL, Access Control List) sur les comptes de stockage pour lesquels l’espace de noms hiérarchique est activé.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 98656f6751ec7ed8d18c4cd4c25715df5d59d011
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109633624"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Utilisez le Kit de développement logiciel (SDK) JavaScript dans Node.js pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser Node.js pour récupérer, définir et mettre à jour les listes de contrôle d’accès des répertoires et des fichiers. 

[Package (Gestionnaire de package Node)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- Azure CLI version `2.6.0` ou ultérieure.

- Une des autorisations de sécurité suivantes :

  - Un [principal de sécurité](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (AD) provisionné qui a reçu le rôle [Propriétaire des données Blob de stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) dans l’étendue du conteneur cible, du groupe de ressources parent ou de l’abonnement.  

  - Utilisateur propriétaire du conteneur ou du répertoire cible auquel vous envisagez d’appliquer les paramètres ACL. Pour définir des listes de contrôle d’accès de façon récursive, cela inclut tous les éléments enfants du conteneur ou du répertoire cible.
  
  - Clé du compte de stockage.

## <a name="set-up-your-project"></a>Configuration de votre projet

Installez la bibliothèque de client Data Lake pour JavaScript en ouvrant une fenêtre de terminal, puis en entrant la commande suivante.

```javascript
npm install @azure/storage-file-datalake
```

Importez le package `storage-file-datalake` en plaçant cette instruction en haut de votre fichier de code. 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Se connecter au compte

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Se connecter avec Azure Active Directory (AD)

> [!NOTE]
> Si vous utilisez Azure Active Directory (Azure AD) pour autoriser l’accès, assurez-vous que le [rôle Propriétaire des données blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) est attribué à votre principal de sécurité. Pour en savoir plus sur l’application des autorisations de liste de contrôle d’accès et les conséquences de leur modification, consultez [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour JS](https://www.npmjs.com/package/@azure/identity) pour authentifier votre application auprès d’Azure AD.

Obtenez un ID client, un secret client et un ID de locataire. Pour ce faire, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](../common/storage-auth-aad-app.md). Dans le cadre de ce processus, vous devrez attribuer l’un des rôles [Azure RBAC (contrôle d’accès en fonction du rôle Azure)](../../role-based-access-control/overview.md) suivants à votre principal de sécurité. 

|Role|Capacité de paramétrage ACL|
|--|--|
|[Propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Tous les répertoires et fichiers du compte.|
|[Contributeur aux données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Seuls les répertoires et les fichiers appartenant au principal de sécurité.|

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Pour obtenir plus d’exemples, consultez la documentation [Bibliothèque de client Azure Identity pour JS](https://www.npmjs.com/package/@azure/identity).

### <a name="connect-by-using-an-account-key"></a>Connexion avec une clé de compte

Il s’agit du moyen le plus simple de se connecter à un compte. 

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’une clé de compte.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> Cette méthode d'autorisation ne fonctionne que pour les applications Node.js. Si vous prévoyez d'exécuter votre code dans un navigateur, vous pouvez l'autoriser à l'aide d'Azure Active Directory (AD).

## <a name="get-and-set-a-directory-acl"></a>Obtenir et définir la liste de contrôle d’accès d’un répertoire

Cet exemple obtient puis définit l’ACL d’un répertoire nommé `my-directory`. Cet exemple donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture.

> [!NOTE]
> Si votre application autorise l’accès en utilisant Azure Active Directory (Azure AD), vérifiez que le [rôle de propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) a été attribué au principal de sécurité utilisé par votre application pour autoriser l’accès. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

Vous pouvez également obtenir et définir la liste de contrôle d’accès du répertoire racine d’un conteneur. Pour obtenir le répertoire racine, transmettez une chaîne vide (`/`) dans la méthode **DataLakeFileSystemClient.getDirectoryClient**.

### <a name="get-and-set-a-file-acl"></a>Obtenir et définir la liste de contrôle d’accès d’un fichier

Cet exemple obtient puis définit l’ACL d’un fichier nommé `upload-file.txt`. Cet exemple donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture.

> [!NOTE]
> Si votre application autorise l’accès en utilisant Azure Active Directory (Azure AD), vérifiez que le [rôle de propriétaire des données Blob du stockage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) a été attribué au principal de sécurité utilisé par votre application pour autoriser l’accès. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>Voir aussi

- [Package (Gestionnaire de package Node)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)
- [Modèle de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listes de contrôle d’accès (ACL) dans Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)