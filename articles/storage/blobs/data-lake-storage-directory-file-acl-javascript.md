---
title: Utiliser JavaScript pour gérer les données dans Azure Data Lake Storage Gen2
description: Utilisez la bibliothèque de client Azure Data Lake Storage pour JavaScript qui permet de gérer les répertoires et les fichiers sur les comptes de stockage pour lesquels l’espace de noms hiérarchique est activé.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 8ce5df805ddce6cdb52e4225bb77e2d8dfa9b9b0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650165"
---
# <a name="use-javascript-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Utiliser JavaScript pour gérer les répertoires et les fichiers dans Azure Data Lake Storage Gen2

Cet article explique comment utiliser JavaScript pour créer et gérer des répertoires et des fichiers sur les comptes de stockage pour lesquels l’espace de noms hiérarchique est activé.

Pour en savoir plus sur la façon d’obtenir, de définir et de mettre à jour les listes de contrôle d’accès (ACL, Access Control List) des répertoires et des fichiers, consultez [Utiliser JavaScript pour gérer les listes de contrôle d’accès dans Azure Data Lake Storage Gen2](data-lake-storage-acl-javascript.md).

[Package (Gestionnaire de package Node)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).

- Un compte de stockage doté d’un espace de noms hiérarchique activé. Pour créer un test, suivez [ces](create-data-lake-storage-account.md) instructions.

- Si vous utilisez ce package dans une application Node.js, vous devez utiliser Node.js 8.0.0 ou version ultérieure.

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

Pour utiliser les extraits de code de cet article, vous devez créer une instance **DataLakeServiceClient** qui représente le compte de stockage. 

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
> Cette méthode d'autorisation ne fonctionne que pour les applications Node.js. Si vous prévoyez d’exécuter votre code dans un navigateur, vous pouvez l’autoriser à l’aide d’Azure Active Directory (Azure AD).

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Se connecter avec Azure Active Directory (Azure AD)

Vous pouvez utiliser la [bibliothèque de client Azure Identity pour JS](https://www.npmjs.com/package/@azure/identity) pour authentifier votre application auprès d’Azure AD.

Cet exemple crée une instance de **DataLakeServiceClient** à l’aide d’un ID client, d’une clé secrète client et d’un ID locataire.  Pour obtenir ces valeurs, consultez [Obtenir un jeton à partir d’Azure AD pour autoriser les requêtes à partir d’une application cliente](../common/storage-auth-aad-app.md).

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

## <a name="create-a-container"></a>Créez un conteneur.

Un conteneur fait office de système de fichiers pour vos fichiers. Pour en créer un, procurez-vous une instance de **FileSystemClient**, puis appelez la méthode **FileSystemClient.Create**.

Cet exemple crée un conteneur nommé `my-file-system`. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Créer un répertoire

Pour créer une référence de répertoire, procurez-vous une instance de **DirectoryClient**, puis appelez la méthode **DirectoryClient.create**.

Cet exemple ajoute un répertoire nommé `my-directory` à un conteneur. 

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

- [Package (Gestionnaire de package Node)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Exemples](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Envoyer des commentaires](https://github.com/Azure/azure-sdk-for-java/issues)