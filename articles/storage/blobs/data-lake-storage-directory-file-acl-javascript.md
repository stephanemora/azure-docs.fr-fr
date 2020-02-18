---
title: Utiliser JavaScript pour les fichiers et les listes de contrôle d'accès dans Azure Data Lake Storage Gen2 (préversion)
description: Utilisez la bibliothèque de client Azure Storage Data Lake pour JavaScript afin de gérer les répertoires, les fichiers et les listes de contrôle d'accès (listes ACL) sur les comptes de stockage sur lesquels l'espace de noms hiérarchique (HNS) est activé.
author: normesta
ms.service: storage
ms.date: 12/18/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 8fd63adc76422b7fd9978e626208aa90593f8604
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154768"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Utiliser JavaScript pour gérer les répertoires, les fichiers et les listes de contrôle d'accès dans Azure Data Lake Storage Gen2 (préversion)

Cet article explique comment utiliser JavaScript pour créer et gérer des répertoires, des fichiers et des autorisations sur les comptes de stockage sur lesquels l'espace de noms hiérarchique (HNS) est activé. 

> [!IMPORTANT]
> La bibliothèque JavaScript qui est présentée dans cet article est actuellement disponible en préversion publique.

[Package (Gestionnaire de package Node)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Conditions préalables requises

> [!div class="checklist"]
> * Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un compte de stockage doté d’un espace de noms hiérarchique (HNS) activé. Pour créer un test, suivez [ces](data-lake-storage-quickstart-create-account.md) instructions.
> * Si vous utilisez ce package dans une application Node.js, vous devez utiliser Node.js 8.0.0 ou version ultérieure.

## <a name="set-up-your-project"></a>Configuration de votre projet

Installez la bibliothèque de client Data Lake pour JavaScript en ouvrant une fenêtre de terminal, puis en entrant la commande suivante.

```javascript
npm install @azure/storage-file-datalake
```

Importez le package `storage-file-datalake` en plaçant cette instruction en haut de votre fichier de code. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Se connecter au compte 

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. Le moyen le plus simple d’en obtenir un consiste à utiliser une clé de compte. 

Cet exemple crée une instance **DataLakeServiceClient** à l’aide d’une clé de compte.

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
> Cette méthode d'autorisation ne fonctionne que pour les applications Node.js. Si vous prévoyez d'exécuter votre code dans un navigateur, vous pouvez l'autoriser à l'aide d'Azure Active Directory (AD). Pour plus d'informations, consultez le fichier Lisez-moi de la [bibliothèque de client Azure Storage File Data Lake pour JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake). 

## <a name="create-a-file-system"></a>Créer un système de fichiers

Un système de fichiers agit comme un conteneur pour vos fichiers. Pour en créer un, procurez-vous une instance de **FileSystemClient**, puis appelez la méthode **FileSystemClient.Create**.

Cet exemple crée un système de fichiers nommé `my-file-system`. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Créer un répertoire

Pour créer une référence de répertoire, procurez-vous une instance de **DirectoryClient**, puis appelez la méthode **DirectoryClient.create**.

Cet exemple ajoute un répertoire nommé `my-directory` dans un système de fichiers. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Renommer ou déplacer un répertoire

Renommez ou déplacez un répertoire en appelant la méthode **DirectoryClient.rename**. Transmettez un paramètre au chemin d’accès du répertoire souhaité. 

Cet exemple renomme un sous-répertoire avec le nom `my-directory-renamed`.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Cet exemple déplace un répertoire nommé `my-directory-renamed` vers un sous-répertoire d’un répertoire nommé `my-directory-2`. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Supprimer un répertoire

Supprimez un répertoire en appelant la méthode **DirectoryClient.delete**.

Cet exemple supprime un répertoire nommé `my-directory`.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Gérer la liste ACL d’un répertoire

Cet exemple obtient puis définit l’ACL d’un répertoire nommé `my-directory`. Cet exemple donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture.

> [!NOTE]
> Si votre application autorise l’accès en utilisant Azure Active Directory (Azure AD), vérifiez que le [rôle de propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) a été attribué au principal de sécurité utilisé par votre application pour autoriser l’accès. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

## <a name="upload-a-file-to-a-directory"></a>Charger un fichier dans un répertoire

Tout d'abord, lisez un fichier. L'exemple suivant utilise le module Node.js `fs`. Créez ensuite une référence de fichier dans le répertoire cible en créant une instance de **FileClient**, puis en appelant la méthode **FileClient.create**. Téléchargez un fichier en appelant la méthode **FileClient.append**. Veillez à procéder au chargement en appelant la méthode **FileClient.flush**.

Cet exemple télécharge un fichier texte dans un répertoire nommé `my-directory`.

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Gérer la liste ACL d’un fichier

Cet exemple obtient puis définit l’ACL d’un fichier nommé `upload-file.txt`. Cet exemple donne à l’utilisateur propriétaire des autorisations de lecture, d’écriture et d’exécution, donne au groupe propriétaire uniquement des autorisations de lecture et d’exécution et donne à tous les autres l’accès en lecture.

> [!NOTE]
> Si votre application autorise l’accès en utilisant Azure Active Directory (Azure AD), vérifiez que le [rôle de propriétaire des données Blob du stockage](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) a été attribué au principal de sécurité utilisé par votre application pour autoriser l’accès. Pour en savoir plus sur l’application des autorisations ACL et les conséquences de leur modification, consultez [Contrôle d’accès dans Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

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

## <a name="download-from-a-directory"></a>Télécharger à partir d’un répertoire

Tout d'abord, créez une instance de **FileSystemClient** représentant le fichier que vous souhaitez télécharger. Utilisez la méthode **FileSystemClient.read** pour lire le fichier. Ensuite, écrivez le fichier. L'exemple utilise le module Node.js `fs` pour effectuer cette opération. 

> [!NOTE]
> Cette méthode de téléchargement de fichier ne fonctionne que pour les applications Node.js. Si vous prévoyez d'exécuter votre code dans un navigateur, consultez le fichier Lisez-moi de la bibliothèque de client [Azure Storage File Data Lake pour JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) pour connaître la procédure à suivre dans un navigateur. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Afficher le contenu du répertoire

Cet exemple imprime les noms des répertoires et fichiers situés dans un répertoire nommé `my-directory`.

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Voir aussi

* [Package (Gestionnaire de package Node)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)